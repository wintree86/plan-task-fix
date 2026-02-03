---
name: plan
description: >
  Plan document manager -- create, modify, and organize plan.md with Phase/task structure.
  기획서(plan.md) 자동 생성/추가/수정 스킬.
  Triggers: /plan, "create plan", "기획서", "plan 생성", "Phase 추가"
---

# Plan Manager

Create, modify, and organize plan.md with Phase/task structure.
plan.md를 자동으로 생성/추가/수정하는 스킬입니다.

## Usage / 사용법

```
/plan              # Summary / plan.md 현황 요약
/plan create       # Create new plan.md / 새 plan.md 생성 (Agent)
/plan add          # Add Phase/tasks / Phase/태스크 추가 (Agent)
/plan edit         # Edit a Phase / 특정 Phase 수정 (Agent)
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

## Command: (default) - Summary / 현황 요약

Summarizes plan.md status.
plan.md 현황을 요약합니다.

**Runs when no arguments are given. / 인수가 없는 경우 실행됩니다.**

### Steps / 수행 단계:
1. Search for plan.md (see search order) / plan.md 파일 탐색
2. Read and analyze file / 파일 읽기 및 분석
3. Parse Phase/task structure / Phase/태스크 구조 파싱
4. Output summary / 현황 출력

### Output format / 출력 형식:
```
📋 plan.md Summary / 현황

📍 Location / 위치: .claude-docs/plan.md
📅 Last Modified / 최종 수정: 2026-02-03

📊 Structure / 구조:
- Phase 1: MVP (5 tasks / 5개 태스크)
- Phase 2: Core Features (5 tasks / 5개 태스크)
- Phase 3: Monetization (4 tasks / 4개 태스크)

Total: 14 tasks / 총 14개 태스크
```

### If no file found / 파일 없는 경우:
```
📋 No plan.md found / plan.md 없음

💡 Create one with / 생성하려면:
   /plan create [description / 요청]
Example / 예: /plan create Tarot app, 5 phases
```

---

## Command: create

Creates a new plan.md.
새 plan.md를 생성합니다.

**Invokes plan-generator Agent. / plan-generator Agent를 호출합니다.**

### Syntax / 구문:
```
/plan create [description / 요청]
```

### Steps / 수행 단계:
1. Invoke plan-generator Agent / plan-generator Agent 호출
2. Analyze request (app type, features, Phase count) / 요청 분석
3. Generate standard plan.md template / 표준 plan.md 템플릿 생성:
   - Project overview / 앱 개요
   - Phase checklists with IDs / Phase별 체크리스트 (ID 포함)
4. Save plan.md to document folder / 문서 폴더에 plan.md 저장

### Output format / 출력 형식:
```
✅ Created / plan.md 생성 완료

📋 Structure / 구조:
- Phase 1: MVP (5 tasks / 5개 태스크)
- Phase 2: Core Features (4 tasks / 4개 태스크)
- Phase 3: Polish (3 tasks / 3개 태스크)

Total: 12 tasks created / 총 12개 태스크 생성
📍 Saved / 저장: .claude-docs/plan.md

💡 Next step / 다음 단계: /task generate
```

---

## Command: add

Adds Phases/tasks to existing plan.md.
기존 plan.md에 Phase/태스크를 추가합니다.

**Invokes plan-generator Agent. / plan-generator Agent를 호출합니다.**

### Syntax / 구문:
```
/plan add [description / 요청]
```

### Examples / 예시:
```
/plan add Add i18n to Phase 2
/plan add Create Phase 5: Performance optimization
/plan add Add payment feature          # Auto-placed in appropriate Phase
/plan add Phase 2에 다국어 지원 기능 추가
/plan add 결제 기능 추가                # 적절한 Phase에 자동 배치
```

### Steps / 수행 단계:
1. Invoke plan-generator Agent / plan-generator Agent 호출
2. Analyze existing plan.md / 기존 plan.md 분석
3. Add tasks per request / 요청에 따라 태스크 추가:
   - Phase specified: add to that Phase / Phase 지정 시: 해당 Phase에 추가
   - No Phase specified: auto-place / Phase 미지정 시: 적절한 Phase에 자동 배치
   - New Phase requested: create section / 새 Phase 요청 시: 새 Phase 섹션 생성
4. Auto-assign IDs (continuing from existing) / ID 자동 부여
5. Update plan.md / plan.md 업데이트

### Output format / 출력 형식:
```
✅ Updated / plan.md 업데이트

➕ Added tasks / 추가된 태스크:
- 2.6 i18n support
- 2.7 Language selection UI
- 2.8 Translation data management

📍 Phase 2: Core Features
   5 → 8 tasks / 5개 → 8개 태스크

💡 Next step / 다음 단계: /task sync
```

---

## Command: edit

Edits a specific Phase.
특정 Phase를 수정합니다.

**Invokes plan-generator Agent. / plan-generator Agent를 호출합니다.**

### Syntax / 구문:
```
/plan edit Phase N
/plan edit [Phase name]
```

### Steps / 수행 단계:
1. Invoke plan-generator Agent / plan-generator Agent 호출
2. Display Phase contents / 해당 Phase 내용 표시
3. AskUserQuestion for edit request / 수정 요청 입력 받기
4. Apply edits and save / 수정 적용 후 저장

### Output format / 출력 형식:
```
📝 Editing / 편집: Phase 2: Core Features

Current tasks / 현재 태스크:
- [ ] 2.1 Three card spread
- [ ] 2.2 Fan card selection UI
- [ ] 2.3 Particle effects
- [ ] 2.4 Ad integration
- [ ] 2.5 Reading history

Enter your edit request... / 수정 요청을 입력하세요...
```

After edit / 수정 후:
```
✅ Edited / 수정 완료

➖ Removed / 삭제: 2.3 Particle effects
➕ Added / 추가: 2.6 Sound effects
✏️ Modified / 수정: 2.1 Three card spread → Three/one card spread
```

---

## plan.md Template / 생성 템플릿

```markdown
# {Project Name}

**Created:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

## Project Overview

{Brief description}

---

## Development Plan

### Phase 1: {Phase Name}

- [ ] 1.1 {Task}
- [ ] 1.2 {Task}
- [ ] 1.3 {Task}

### Phase 2: {Phase Name}

- [ ] 2.1 {Task}
- [ ] 2.2 {Task}

[...]
```

### Key Rules / 핵심 규칙
1. **IDs required** - All tasks get `N.M` format ID / 모든 태스크에 `N.M` 형식 ID 부여
2. **Checklist format** - Standard markdown `- [ ]` / 표준 마크다운 사용
3. **Phase heading** - `### Phase N:` format / `### Phase N:` 형식 유지
4. **Date management** - Auto-update created/modified dates / 생성일/수정일 자동 갱신

---

## Plan File Search Order / plan 파일 탐색 순서

1. `.claude-docs/plan.md`
2. `.claude/docs/plan.md`
3. `docs/plan.md`
4. `PLAN.md`
5. `plan.md`

---

## Agent Instructions / Agent 지시사항

### Context to pass to plan-generator Agent / Agent 호출 시 전달할 컨텍스트:

```
Command: {create|add|edit}
Request: {user input}
Project root: {CWD or git root}
Document folder: {detected document folder path}
Existing plan.md: {full content if exists}
```

### plan-generator Agent roles / Agent 역할:
1. `create`: Analyze request → generate full plan.md / 요청 분석 → plan.md 전체 생성
2. `add`: Analyze existing plan.md → add tasks/Phases / 기존 분석 → 태스크/Phase 추가
3. `edit`: Display Phase → get user input → apply edits / Phase 표시 → 입력 받기 → 수정 적용

---

## Command Reference / 명령어 정리

| Command | Action | Agent | Purpose |
|---------|--------|-------|---------|
| `/plan` | Summary / 현황 요약 | No | Check plan.md status / 상태 확인 |
| `/plan create` | Create plan.md / 생성 | Yes | Start project / 프로젝트 시작 |
| `/plan add` | Add tasks / 추가 | Yes | Add features / 기능 추가 |
| `/plan edit` | Edit Phase / 수정 | Yes | Change plan / 기획 변경 |

---

## Integration with `/task` / `/task` 스킬과 연계

```
/plan create "My App"       → plan.md created / 생성
        ↓
/task generate              → task.md created / 생성
        ↓
[Development / 개발 진행]
        ↓
/plan add "New feature"     → plan.md updated / 태스크 추가
        ↓
/task sync                  → task.md synced / 동기화 (completed status preserved / 완료 상태 유지)
```

---

## Related Skills

Part of the **plan-task-fix** suite:
- `/plan` - Plan document management (plan.md) / 기획서 관리
- `/task` - Task tracking and progress (task.md) / 태스크 추적
- `/fix` - Bug/improvement backlog (backlog.md) / 백로그 관리

Install: `npx skills add <owner>/plan-task-fix`
