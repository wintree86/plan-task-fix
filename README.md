# plan-task-fix

> Plan, track, and fix — markdown-based project management for AI coding agents.
> 기획, 추적, 수정 — AI 코딩 에이전트를 위한 마크다운 기반 프로젝트 관리.

## Skills / 스킬 목록

| Skill | Command | Purpose |
|-------|---------|---------|
| **plan** | `/plan` | Create & manage plan.md / 기획서 관리 |
| **task** | `/task` | Track tasks from plan / 태스크 추적 |
| **fix** | `/fix` | Bug & improvement backlog / 백로그 관리 |

## Install / 설치

```bash
# Install all 3 skills / 전체 설치
npx skills add <owner>/plan-task-fix

# Install individual skill / 개별 설치
npx skills add <owner>/plan-task-fix --skill plan
npx skills add <owner>/plan-task-fix --skill task
npx skills add <owner>/plan-task-fix --skill fix
```

## Workflow / 워크플로우

```
/plan create "My App"     →  plan.md created
        ↓
/task generate            →  task.md created
        ↓
   [Development]
        ↓
/task done                →  Mark completed tasks
        ↓
/fix bug "keyboard issue" →  Log bugs as you find them
        ↓
/plan add "new feature"   →  Evolve the plan
        ↓
/task sync                →  Sync task.md (preserves progress)
        ↓
/task                     →  Check overall progress
```

### Files Generated / 생성되는 파일

```
.claude-docs/
├── plan.md        # Project plan with Phases & tasks
├── task.md        # Task list with progress tracking
├── progress.md    # Progress change log
├── backlog.md     # Bugs, improvements, tech debt
└── archived.md    # Archived completed tasks (auto-generated)
```

## Quick Reference / 명령어 요약

### `/plan` - Plan Manager / 기획서 관리

| Command | Description |
|---------|-------------|
| `/plan` | Show plan summary / plan.md 현황 요약 |
| `/plan create [desc]` | Create new plan.md / 새 plan.md 생성 |
| `/plan add [desc]` | Add Phases/tasks / Phase/태스크 추가 |
| `/plan edit Phase N` | Edit a Phase / 특정 Phase 수정 |

### `/task` - Task Tracker / 태스크 추적

| Command | Description |
|---------|-------------|
| `/task` | Show progress summary / 현황 요약 |
| `/task generate` | Generate task.md from plan / task.md 생성 |
| `/task update` | Recalculate progress / 진행률 업데이트 |
| `/task done` | Mark tasks complete / 완료 처리 |
| `/task verify` | Verify implementation / 구현 검증 |
| `/task diff` | Preview plan vs task changes / 변경사항 미리보기 |
| `/task sync` | Sync task.md from plan / plan.md → task.md 동기화 |

### `/fix` - Backlog Manager / 백로그 관리

| Command | Description |
|---------|-------------|
| `/fix` | Show open items / 미해결 목록 |
| `/fix "desc"` | Auto-classify and add / 자동 분류 등록 |
| `/fix bug "desc"` | Add bug / 버그 등록 |
| `/fix improve "desc"` | Add improvement / 개선 등록 |
| `/fix debt "desc"` | Add tech debt / 기술부채 등록 |
| `/fix done [ID]` | Mark complete / 완료 처리 |
| `/fix clean` | Remove completed / 완료 항목 정리 |

## Examples / 예시

See the [`examples/`](./examples/) directory for sample output:
- [`plan.md`](./examples/plan.md) - Sample project plan
- [`task.md`](./examples/task.md) - Sample task list with progress table
- [`backlog.md`](./examples/backlog.md) - Sample backlog with bugs, improvements, tech debt

## Compatibility / 호환성

Designed for AI coding agents that support markdown-based skills:
- **Claude Code** (Anthropic)
- **Cursor**
- **Windsurf**
- **GitHub Copilot**
- Any agent supporting `SKILL.md` format

## Bilingual Support / 이중언어 지원

All output messages are bilingual (English / Korean).
모든 출력 메시지는 한영 병기로 표시됩니다.

```
✅ Created / 생성 완료
📍 Location / 위치: .claude-docs/plan.md
📊 Progress / 진행률: 8/12 (67%)
🐛 Bugs (2 open / 2개 미해결)
```

## License

[MIT](./LICENSE)
