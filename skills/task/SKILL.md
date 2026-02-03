---
name: task
description: >
  Task tracker -- generate task.md from plan, track progress, verify implementation.
  범용 프로젝트 태스크 관리. plan 문서에서 task.md 생성, 진행률 추적, 현황 요약.
  Triggers: /task, "task", "태스크", "progress", "진행률", "진행 상황"
---

# Task Manager

Track tasks from plan.md, manage progress, verify implementation.
plan.md를 기반으로 task.md를 생성하고 진행률을 추적합니다.

## Usage / 사용법

```
/task              # Summary / 현황 요약 (default)
/task summary      # Summary / 현황 요약
/task generate     # Generate task.md / task.md 생성
/task update       # Update progress / 진행률 업데이트
/task done         # Mark done / 완료 처리 (Agent)
/task verify       # Verify implementation / 구현 검증 (Agent)
/task diff         # Preview changes / plan.md ↔ task.md 변경사항 미리보기
/task sync         # Sync from plan / plan.md 수정 후 task.md 동기화
```

The first argument determines the command: `$ARGUMENTS`

---

## Project Detection / 프로젝트 감지 규칙

1. **CWD-based** - Uses current working directory as project root / 현재 작업 디렉토리를 프로젝트 루트로 사용
2. **Git root detection** - `git rev-parse --show-toplevel` / Git root로 프로젝트 루트 확인
3. **Project name** - Git root folder name / git root 폴더명 사용

### Document folder search order / 문서 폴더 탐색 순서:
1. `.claude-docs/`
2. `.claude/docs/`
3. `docs/`
4. CWD (project root / 프로젝트 루트)

---

## Command: generate

Generates task.md from plan document.
plan 문서에서 task.md를 생성합니다.

### Plan file search order / plan 파일 탐색 순서:
1. `.claude-docs/plan.md`
2. `.claude/docs/plan.md`
3. `docs/plan.md`
4. `PLAN.md`
5. `plan.md`

### Task extraction rules / 태스크 추출 규칙:

**Checklist pattern detection / 체크리스트 패턴 자동 감지:**
- `- [ ]` incomplete task / 미완료 태스크
- `- [x]` completed task / 완료된 태스크

**Phase heading detection / Phase/단계 헤딩 자동 파싱:**
- `## Phase N:`, `### Phase N:`
- `## Step N:`, `### Step N:`
- `## N.`, `### N.` (headings starting with number / 숫자로 시작하는 헤딩)

### Steps / 수행 단계:
1. Search and read plan file / plan 파일 탐색 및 읽기
2. Extract sections with checklist patterns / 체크리스트 패턴이 있는 섹션 추출
3. Group tasks by Phase / Phase/단계별로 태스크 그룹화
4. Create `task.md` in document folder / 문서 폴더에 `task.md` 생성

### Generated task.md format / 생성되는 task.md 형식:
```markdown
# {Project Name} Task List

**Last Updated:** YYYY-MM-DD

## Progress Overview

| Phase | Name | Status | Progress |
|-------|------|--------|----------|
| 1 | MVP | pending | 0/5 (0%) |
| 2 | Core Features | pending | 0/5 (0%) |
...

## Phase 1: MVP
**Status:** pending

- [ ] Task 1
- [ ] Task 2

## Phase 2: Core Features
**Status:** pending

- [ ] Task 3
- [ ] Task 4
```

---

## Command: update

Updates task.md progress.
task.md의 진행률을 업데이트합니다.

### Steps / 수행 단계:
1. Read task.md / task.md 파일 읽기
2. Count checked items `[x]` / 체크된 항목 카운트
3. Update Progress Overview table / Progress Overview 테이블 업데이트
4. Update Phase Status / Phase Status 업데이트:
   - All incomplete: `pending` / 모든 태스크 미완료
   - Some complete: `in-progress` / 일부 완료
   - All complete: `completed` / 모두 완료
5. Update `Last Updated` date / 날짜 갱신
6. Append change log to progress.md / progress.md에 변경 이력 추가

### progress.md format / progress.md 형식:
```markdown
# Progress Log

## YYYY-MM-DD
- Phase N: X/Y completed / 완료 (Z%)
- Completed / 완료된 태스크: Task1, Task2
- Next / 다음 태스크: Task3

## YYYY-MM-DD (previous / 이전)
...
```

---

## Command: summary (default / 기본)

Summarizes current progress.
현재 진행 현황을 요약합니다.

**Runs when no arguments or `summary`. / 인수가 없거나 `summary`인 경우 실행됩니다.**

### Output format / 출력 형식:
```
📊 {Project Name} Progress / 개발 현황

Overall / 전체 진행률: 12/29 (41%)

Phase 1: MVP ✅ Completed / 완료 (5/5)
Phase 2: Core Features 🔄 In Progress / 진행중 (3/5)
Phase 3: Advanced ⏳ Pending / 대기 (0/4)

🔄 In Progress / 현재 진행 중:
- Remaining tasks in active Phase

📋 Next Tasks / 다음 태스크:
1. Next task 1
2. Next task 2
3. Next task 3
```

### Status icons / 상태 아이콘:
- ✅ Completed / 완료 (completed)
- 🔄 In Progress / 진행중 (in-progress)
- ⏳ Pending / 대기 (pending)

---

## File Location Rules / 파일 위치 규칙

| File | Default Location | Description |
|------|-----------------|-------------|
| plan.md | document folder | Source plan / 원본 기획서 |
| task.md | document folder | Generated task list / 태스크 목록 |
| progress.md | document folder | Progress log / 진행 이력 |

Creates `.claude-docs/` automatically if no document folder exists.
문서 폴더가 없으면 `.claude-docs/`를 자동 생성합니다.

---

## Notes / 주의사항

1. **Direct editing** - Change `[ ]` to `[x]` manually / task.md 직접 편집 가능
2. **Update after changes** - Run `/task update` to refresh tables / 진행률 변경 후 테이블 갱신
3. **plan.md format** - Requires checklist (`- [ ]`) patterns / 체크리스트 패턴 필요
4. **Use sync after plan changes** - `/task generate` resets status; use `/task sync` instead / 기획서 수정 후에는 `/task sync` 사용
5. **Auto-archive** - Deleted completed tasks are archived to archived.md / 완료된 태스크 삭제 시 archived.md에 보관

---

## Command: done

Auto-detects completed tasks from current session and updates task.md.
현재 세션에서 완료된 태스크를 자동 감지하여 task.md를 업데이트합니다.

**Invokes task-tracker Agent. / task-tracker Agent를 호출합니다.**

### Steps / 수행 단계:
1. Invoke task-tracker Agent / task-tracker Agent 호출
2. Analyze conversation (Edit/Write tool history) / 대화 내용 분석
3. Identify completed tasks / 완료된 태스크 식별
4. Update task.md (`[ ]` → `[x]`) / task.md 업데이트
5. Recalculate Progress Overview / Progress Overview 테이블 재계산
6. Update Last Updated date / 날짜 갱신

### Output format / 출력 형식:
```
✅ Done / 완료: [Task name]
📊 Progress / 진행률: Phase N - X/Y (Z%)

task.md updated / 업데이트 완료
```

### Example / 사용 예시:
```
Developer: "Implement card selection screen"
Claude: [code written]

Developer: "/task done"
Claude:
  ✅ Done / 완료: 2.2 Three Card Selection
  📊 Phase 2: 1/6 (17%)
  task.md updated / 업데이트 완료
```

---

## Command: verify

Compares plan.md requirements against actual code implementation.
plan.md와 실제 코드를 비교하여 구현 상태를 검증합니다.

**Invokes task-tracker Agent. / task-tracker Agent를 호출합니다.**

### Steps / 수행 단계:
1. Invoke task-tracker Agent / task-tracker Agent 호출
2. Extract plan.md requirements / plan.md 요구사항 추출
3. Inspect code files (Glob, Grep, Read) / 코드 파일 검사
4. Report implementation status / 구현 상태 보고

### Verification criteria / 검증 기준:
- ✅ Completed / 완료: File exists + core logic implemented / 파일 존재 + 핵심 로직 구현
- 🔄 In Progress / 진행중: File exists but partial / 파일 존재하지만 일부만 구현
- ❌ Not Started / 미시작: No file or empty / 파일 없음 또는 빈 구현

### Output format / 출력 형식:
```
📋 Phase N: [Phase Name] Implementation Status / 구현 현황

✅ Completed / 완료:
- Task 1 (lib/path/file.dart)

🔄 In Progress / 진행중:
- Task 2 (lib/path/file2.dart - partial / 일부 구현)

❌ Not Started / 미시작:
- Task 3

📊 Verification / 검증 결과: 1/3 completed / 완료 (33%)
```

---

## Command: diff

Previews changes between plan.md and task.md before syncing.
동기화 전 plan.md와 task.md의 변경사항을 미리보기합니다.

### Steps / 수행 단계:
1. Parse plan.md (extract Phases/tasks) / plan.md 파싱
2. Parse task.md (current tasks and status) / task.md 파싱
3. Compare and analyze / 두 파일 비교 분석
4. Report changes / 변경사항 보고

### Task matching criteria / 태스크 매칭 기준:
1. **ID-based (priority)** - Match by `1.1`, `2.3` numbers / ID 번호로 정확히 매칭
2. **Similarity-based (fallback)** - 70%+ text similarity / 텍스트 70% 이상 유사

### Output format / 출력 형식:
```
📋 plan.md ↔ task.md Comparison / 비교

➕ Added / 추가 (2):
- 2.5 New feature
- 3.1 Additional feature

➖ Removed / 삭제 (1):
- 2.3 Old feature [incomplete - removed / 미완료 - 삭제됨]

✏️ Modified / 수정 (1):
- 2.4 "Feature A" → "Feature A (improved)" [status preserved / 완료 상태 유지]

🗄️ To Archive / 보관 예정 (1):
- 1.3 Removed feature [completed - to archived.md / 완료됨 - archived.md로 이동]
```

---

## Command: sync

Syncs task.md after plan.md changes. **Preserves completion status.**
plan.md 수정 후 task.md를 동기화합니다. **기존 완료 상태가 유지됩니다.**

### Steps / 수행 단계:
1. Backup task.md → task.md.bak / 백업
2. Parse plan.md (extract Phases/tasks) / plan.md 파싱
3. Compare with task.md / task.md와 비교
4. Apply changes / 변경사항 적용:
   - New tasks: add as `- [ ]` / 새 태스크 추가
   - Deleted incomplete: remove / 삭제된 미완료 태스크 삭제
   - Deleted completed: **move to archived.md** / 삭제된 완료 태스크 archived.md로 이동
   - Modified: update text only, preserve status / 텍스트만 업데이트, 상태 유지
5. Recalculate Progress Overview / 테이블 재계산
6. Update Last Updated / 날짜 갱신

### Task matching criteria / 태스크 매칭 기준:
1. **ID-based (priority)** - Match by number / ID 번호로 매칭
2. **Similarity-based (fallback)** - 70%+ text match / 텍스트 70% 이상 유사

### Output format / 출력 형식:
```
🔄 Synced / 동기화 완료

➕ Added / 추가: 2
➖ Removed / 삭제: 1
✏️ Modified / 수정: 1
🗄️ Archived / 보관: 1

📊 Progress / 진행률: 8/12 (67%)
💾 Backup / 백업: task.md.bak
```

### archived.md format / archived.md 형식:
Preserves deleted completed tasks as a log.
삭제되었지만 완료된 태스크를 로그 형태로 보관합니다.

```markdown
# Archived Tasks Log

Completed tasks removed due to plan changes.
기획 변경으로 삭제된 완료 태스크 이력.

---

## YYYY-MM-DD
**Sync reason / 동기화 사유**: Phase N plan change / 기획 변경

| Phase | Task | Completed / 완료일 | Reason / 삭제 사유 |
|-------|------|-------------------|-------------------|
| 1 | 1.3 Removed feature | 2026-01-15 | Plan change / 기획 변경 |
```

### Usage example / 사용 예시:
```
# 1. Preview changes after editing plan.md
/task diff

# 2. Apply sync
/task sync

# 3. Check result
/task
```

---

## Command Reference / 명령어 비교

| Command | Action | Agent | Purpose |
|---------|--------|-------|---------|
| `/task` | Summary / 현황 요약 | No | Quick status check / 빠른 상태 확인 |
| `/task summary` | Summary / 현황 요약 | No | Quick status check / 빠른 상태 확인 |
| `/task generate` | Generate task.md / 생성 | No | Initial setup / 최초 설정 |
| `/task update` | Recalculate progress / 진행률 재계산 | No | After manual checks / 수동 체크 후 |
| `/task done` | Mark complete / 완료 처리 | Yes | After development / 개발 완료 후 |
| `/task verify` | Verify implementation / 구현 검증 | Yes | Code vs plan / 코드 vs 계획 비교 |
| `/task diff` | Preview changes / 변경 미리보기 | No | Before sync / 동기화 전 확인 |
| `/task sync` | Sync execution / 동기화 실행 | No | After plan edit / 기획서 수정 후 |

---

## Related Skills

Part of the **plan-task-fix** suite:
- `/plan` - Plan document management (plan.md) / 기획서 관리
- `/task` - Task tracking and progress (task.md) / 태스크 추적
- `/fix` - Bug/improvement backlog (backlog.md) / 백로그 관리

Install: `npx skills add wintree86/plan-task-fix`
