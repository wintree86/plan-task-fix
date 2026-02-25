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
npx skills add wintree86/plan-task-fix

# Install individual skill / 개별 설치
npx skills add wintree86/plan-task-fix --skill plan
npx skills add wintree86/plan-task-fix --skill task
npx skills add wintree86/plan-task-fix --skill fix
```

## Workflow / 워크플로우

```
/plan create "My App"     →  plan.md created
        ↓
   [Development]
        ↓
/task done                →  Mark completed tasks directly in plan.md
        ↓
/fix bug "keyboard issue" →  Log bugs to backlog.md as you find them
        ↓
/plan add "new feature"   →  Evolve the plan
        ↓
/task                     →  Check overall progress
        ↓
/task wrap                →  End session, archive tasks, extract knowledge, & suggest next focus
```

### Files Generated / 생성되는 파일

```
.claude-docs/
├── plan.md        # Source of truth: Plan and progress tracking / 기획 및 진척도 원본
├── progress.md    # Progress change log / 진척도 변경 로그
├── backlog.md     # Bugs, improvements, tech debt / 백로그
├── archive.md     # Archived completed tasks (auto-wrapped) / 랩업으로 아카이빙된 태스크
└── knowledge.md   # Extracted learnings and decisions / 추출된 팁과 아키텍처 결정(TIL)
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
| `/task update` | Recalculate progress / 진행률 업데이트 |
| `/task done` | Mark tasks complete / 완료 처리 |
| `/task verify` | Verify implementation / 구현 검증 |
| `/task wrap` | Run the 2-phase Multi-Agent Session Wrap-up / 세션 랩업 파이프라인 (아카이빙 및 지식 추출) |

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
- [`plan.md`](./examples/plan.md) - Sample project plan with progress tracking
- [`backlog.md`](./examples/backlog.md) - Sample backlog with bugs, improvements, tech debt

## Compatibility / 호환성

Designed for AI coding agents that support markdown-based skills:
- **Claude Code** (Anthropic)
- **Cursor**
- **Windsurf**
- **GitHub Copilot**
- Any agent supporting `SKILL.md` format

## Advanced Setup (Optional) / 고급 설정 (선택)

Each skill package includes standalone agent files (`plan-generator.md`, `task-tracker.md`).
These are **not required** -- all logic is already embedded in `SKILL.md` files.

각 스킬 패키지에 standalone agent 파일이 포함되어 있습니다.
이 파일들은 **필수가 아닙니다** -- 모든 로직이 이미 SKILL.md에 내장되어 있습니다.

If you want to use them as subagents via Claude Code's Task tool:
Claude Code의 Task 도구로 subagent로 사용하려면:

```bash
# Copy agent files to ~/.claude/agents/
cp ~/.agents/skills/plan/plan-generator.md ~/.claude/agents/
cp ~/.agents/skills/task/task-tracker.md ~/.claude/agents/
```

### Installed File Structure / 설치 파일 구조

```
~/.agents/skills/
├── plan/
│   ├── SKILL.md              # Skill definition (self-contained)
│   └── plan-generator.md     # Optional standalone agent
├── task/
│   ├── SKILL.md              # Skill definition (self-contained)
│   ├── task-tracker.md       # Optional standalone agent
│   └── agents/               # Subagents for `/task wrap` parallel analysis
│       ├── progress-analyzer.md
│       ├── context-archiver.md
│       ├── knowledge-extractor.md
│       ├── next-planner.md
│       └── wrap-coordinator.md
└── fix/
    └── SKILL.md              # Skill definition (no agent needed)
```

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
