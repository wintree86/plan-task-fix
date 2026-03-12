---
name: plan
description: >
  Plan document manager for markdown-based projects. Create, extend, summarize,
  and revise plan.md with Phase/task structure, progress tables, and stable task IDs.
  Use for /plan, creating a plan, adding phases or tasks, or editing project plans.
---

# Plan Manager

Manage `plan.md` as the live project plan.

## Usage

```text
/plan
/plan create [description]
/plan add [description]
/plan edit Phase N
```

If an edit request is incomplete, ask the user a short plain-text follow-up question. Do not rely on environment-specific interactive tools.

## File Detection

Project root:
1. Current working directory
2. Git root if available

Document folder search order:
1. `.claude-docs/`
2. `.claude/docs/`
3. `docs/`
4. Project root

`plan.md` search order:
1. `.claude-docs/plan.md`
2. `.claude/docs/plan.md`
3. `docs/plan.md`
4. `PLAN.md`
5. `plan.md`

If no document folder exists, create `.claude-docs/`.

## Canonical `plan.md` Format

Use this schema consistently:

```markdown
# {Project Name}

**Created:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

## Project Overview

{2-4 lines}

## Progress Overview

| Phase | Name | Status | Progress |
|-------|------|--------|----------|
| 1 | MVP | pending | 0/3 (0%) |

**Overall: 0/3 (0%)**

---

## Development Plan

### Phase 1: MVP
**Status:** pending

- [ ] 1.1 Define scope
- [ ] 1.2 Build initial flow
- [ ] 1.3 Add basic validation
```

Rules:
- Task IDs use `N.M` and never change after creation.
- Phase headings use `### Phase N: Name`.
- Phase status must be one of `pending`, `in-progress`, `completed`, `archived`.
- Archived phases may replace detailed checklists with a short note:

```markdown
### Phase 1: MVP
**Status:** archived

Archived to `archive.md` on YYYY-MM-DD. Summary: core setup and MVP flow completed.
```

## Commands

### `/plan`

Summarize the plan:
- file location
- phase list
- overall progress
- current active or next phase

### `/plan create [description]`

Create a new `plan.md` using the canonical schema.

Generation rules:
- Default to 3-5 phases if the user does not specify a count.
- Order phases from MVP to polish.
- Keep tasks concrete and small enough for one focused work session.
- Include the `Progress Overview` table from the start.

### `/plan add [description]`

Add tasks or phases.

Rules:
- If a phase is specified, append there.
- If a new phase is requested, create a new section and matching table row.
- If no phase is specified, place tasks in the most appropriate non-archived phase.
- Continue numbering from the last task in that phase.
- Refresh `Last Updated`.

### `/plan edit Phase N`

Revise one phase.

Rules:
- Show the current phase contents first.
- If the user has not said what to change yet, ask one short follow-up question in plain text.
- Preserve existing task IDs for unchanged tasks.
- Use new IDs only for newly added tasks.

## Output Guidance

Keep responses short and operational:
- what changed
- affected phase
- updated counts
- suggested next command such as `/task update`

## Notes

- `plan.md` is the live plan, not the long-term memory store.
- Use `plan.md` for committed, scheduled work. Use `backlog.md` for issues and ideas that are not yet promoted into the plan.
- Completed phase detail may move to `archive.md`, but phase rows in `Progress Overview` must remain.
- `task` and `wrap` depend on this schema staying stable.
