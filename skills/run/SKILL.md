---
name: run
description: "User Test 전체 실행 — setup → start → enhance를 한 번에 수행. /user-test:run <target> [preset] [count]"
version: 1.0.0
argument-hint: "<target> [preset-name] [persona-count]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash", "Agent", "WebFetch"]
---

# User Test Run (원스톱)

setup → start → enhance를 한 번에 실행한다.

## 입력 파싱

`$ARGUMENTS`를 파싱한다:
- 첫 번째 인자 (필수): 테스트 대상 (파일 경로, URL, API 엔드포인트)
- 두 번째 인자 (선택): 프리셋 이름 (기본: `diverse-mix`)
- 세 번째 인자 (선택): 페르소나 수

인자가 없거나 부족하면 대화형으로 질문한다.

## 실행 절차

### Phase 1: Setup

1. 프리셋이 지정되면 해당 프리셋을 로드한다
2. 프리셋이 없으면 사용 가능한 프리셋 목록을 보여주고 선택받는다
3. `.user-test/personas.json`에 저장

사용자에게 구성된 그룹을 보여주고 계속 진행할지 확인한다.

### Phase 2: Start

1. 테스트 대상 유형을 판별한다 (spec / web / api)
2. 각 페르소나 에이전트를 병렬 spawn한다
3. 피드백을 수합하여 보고서를 생성한다
4. `.user-test/report-{timestamp}.md`에 저장

보고서 요약을 보여주고 개선 단계로 넘어갈지 확인한다.

### Phase 3: Enhance

1. 보고서에서 개선 권고사항을 추출한다
2. 우선순위별로 정리하여 보여준다
3. 사용자가 범위를 선택하면 수정을 적용한다
4. 변경 요약을 보여준다

## 중단 지점

각 Phase 완료 시 사용자 확인을 받는다. 사용자가 중단하면 해당 시점까지의 결과물은 보존된다:
- Phase 1 후 중단 → `.user-test/personas.json` 보존
- Phase 2 후 중단 → 보고서 보존, `/user-test:enhance`로 나중에 이어서 가능
- Phase 3 완료 → 전체 프로세스 완료

## 완료 메시지

```
✅ User Test 완료!

📊 테스트 요약
- 대상: {target}
- 페르소나: {count}명 ({preset})
- 모드: {mode}

📋 산출물
- 페르소나 설정: .user-test/personas.json
- 피드백 보고서: .user-test/report-{timestamp}.md
- 적용된 개선: {n}건 (P0: {n}, P1: {n})

🔄 재테스트하려면: /user-test:start {target}
```
