# Repository Guidelines

## Project Structure & Module Organization

This repository packages three markdown-based agent skills under [`skills/`](/Users/etudeofsun/dev/projects/toy/plan-task-fix/skills): `plan`, `task`, and `fix`. Each skill is anchored by a `SKILL.md` file; some also include supporting agent docs such as `plan-generator.md` or files in `skills/task/agents/`. Use [`examples/`](/Users/etudeofsun/dev/projects/toy/plan-task-fix/examples) for sample generated outputs like `plan.md` and `backlog.md`. Keep top-level docs limited to project-facing files such as [`README.md`](/Users/etudeofsun/dev/projects/toy/plan-task-fix/README.md), [`LICENSE`](/Users/etudeofsun/dev/projects/toy/plan-task-fix/LICENSE), and this guide.

## Build, Test, and Development Commands

There is no compiled build step. Typical work is editing markdown and validating install and usage flows.

- `npx skills add wintree86/plan-task-fix` installs all skills locally for verification.
- `npx skills add wintree86/plan-task-fix --skill plan` installs a single skill during focused testing.
- `rg --files skills examples` lists tracked skill and example files quickly.
- `git diff -- README.md skills examples` reviews documentation changes before commit.

## Coding Style & Naming Conventions

Write documentation in concise Markdown with ATX headings (`#`, `##`) and short bilingual explanations only where already established. Preserve existing file names and casing: `SKILL.md` for skill entry points, lowercase directories like `skills/task/agents/`, and lowercase example docs such as `examples/plan.md`. Prefer short command examples and ordered steps over long prose. Keep indentation consistent with existing files: two spaces for nested list content in Markdown when needed.

## Testing Guidelines

Testing is manual and example-driven. After editing a skill, verify its command syntax, file search order, and output examples against the corresponding `SKILL.md`. If behavior changes, update the relevant sample files in [`examples/`](/Users/etudeofsun/dev/projects/toy/plan-task-fix/examples). No coverage gate exists, so PRs should note what scenarios were manually checked.

## Commit & Pull Request Guidelines

Follow the existing Conventional Commit pattern seen in history: `feat:`, `fix:`, `docs:`, `refactor:`. Keep scopes descriptive and user-facing, for example `feat: add wrap-up subagents to task skill`. PRs should include a short summary, affected skill paths, example output changes if any, and linked issues when applicable. Include screenshots only when documenting rendered UI from external tooling.
