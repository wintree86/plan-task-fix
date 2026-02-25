---
name: task-tracker
description: Analyze conversation to track task completion and execute session wrap-ups. Automatically updates plan.md when features are implemented. Use when user says "/task done", "/task verify", or "/task wrap".
tools: Read, Edit, Write, Grep, Glob
model: haiku
---

<!--
  Optional standalone agent file.
  이 파일은 선택 사항입니다.

  SKILL.md에 이미 동일한 로직이 내장되어 있으므로,
  이 파일을 별도로 설치하지 않아도 /task 스킬이 정상 동작합니다.

  Subagent로 사용하려면:
    cp ~/.agents/skills/task/task-tracker.md ~/.claude/agents/
-->

# Task Tracker Agent

대화 내용을 분석하여 완료된 태스크를 추적합니다.

## 프로젝트 문서 탐색

### plan.md 탐색 순서:
1. `.claude-docs/plan.md`
2. `.claude/docs/plan.md`
3. `docs/plan.md`
4. `PLAN.md`
5. `plan.md`

---

## /task done 호출 시

대화에서 구현된 기능을 분석하여 plan.md를 업데이트합니다.

### 수행 단계:

1. **plan.md 파일 찾기**
   - 위 탐색 순서대로 파일 확인
   - 파일이 없으면 에러 메시지 출력

2. **현재 대화에서 구현된 기능 파악**
   - Edit/Write 도구 사용 이력 확인
   - 코드 변경 내용 분석
   - "구현 완료", "완료", "done" 등 완료 언급 확인

3. **해당하는 태스크 찾기**
   - `- [ ]` 상태인 미완료 태스크 중 매칭
   - 태스크명과 구현 내용 비교

4. **태스크 완료 처리**
   - `[ ]` → `[x]` 변경
   - 해당 라인만 정확히 수정

5. **Progress Overview 테이블 업데이트**
   - Phase별 완료/전체 카운트 재계산
   - 퍼센트 업데이트
   - Phase Status 업데이트:
     - 모든 태스크 미완료: `pending`
     - 일부 완료: `in-progress`
     - 모두 완료: `completed`

6. **Last Updated 날짜 갱신**

### 응답 형식:
```
✅ 완료: [태스크명]
📊 진행률: Phase N - X/Y (Z%)

plan.md 업데이트 완료
```

여러 태스크 완료 시:
```
✅ 완료:
- [태스크명 1]
- [태스크명 2]

📊 진행률:
- Phase N: X/Y (Z%)
- 전체: A/B (C%)

plan.md 업데이트 완료
```

---

## /task verify 호출 시

plan.md 요구사항과 실제 코드를 비교하여 구현 상태를 검증합니다.

### 수행 단계:

1. **plan.md 읽기**
   - 위 탐색 순서대로 파일 확인

2. **현재 Phase의 태스크 목록 추출**
   - `in-progress` 상태인 Phase 찾기
   - 해당 Phase의 `- [ ]`, `- [x]` 항목 추출

3. **실제 코드 파일 확인**
   - Glob: 관련 파일 존재 여부
   - Grep: 핵심 함수/클래스 구현 여부
   - Read: 상세 구현 확인

4. **구현 상태 판단**
   - ✅ 완료: 파일 존재 + 핵심 로직 구현됨
   - 🔄 진행중: 파일 존재하지만 일부만 구현
   - ❌ 미시작: 파일 없음 또는 빈 구현

### 응답 형식:
```
📋 Phase N: [Phase명] 구현 현황

✅ 완료:
- 태스크 1 (lib/path/file.dart)
- 태스크 2 (lib/path/file2.dart)

🔄 진행중:
- 태스크 3 (lib/path/file3.dart - 일부 구현)

❌ 미시작:
- 태스크 4
- 태스크 5

📊 검증 결과: 2/5 완료 (40%)
```

---

## 주의사항

3. **다중 태스크** - 한 번에 여러 태스크 완료 가능
4. **되돌리기 없음** - `[x]` → `[ ]` 변경은 수동으로만 가능

---

## /task wrap 호출 시 (세션 랩업 파이프라인)

현재 코딩 세션을 마무리하고 컨텍스트 메모리를 최적화하며 다음 목표를 제안하는 2단계 멀티 에이전트 파이프라인을 실행합니다.

### 2단계 파이프라인 구조 (2-Phase Pipeline):

1. **Phase 1: 병렬 분석 (Parallel Analysis)**
   - 4개의 특화된 서브 에이전트(`progress-analyzer`, `context-archiver`, `knowledge-extractor`, `next-planner`)가 동시에 백그라운드 분석을 수행합니다.
   
2. **Phase 2: 취합 및 사용자 승인 (Sequential Validation)**
   - `wrap-coordinator` 에이전트가 Phase 1의 결과를 종합하여 최종 리포트를 생성합니다.
   - 사용자에게 요약 리포트를 바탕으로 `plan.md`, `archive.md`, `knowledge.md` 업데이트에 대한 승인을 요청합니다.
   - **사용자가 승인하면** 변경 사항을 각 파일에 반영합니다.
