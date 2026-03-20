---
name: setup
description: "User Test 페르소나 그룹 구성 — 프리셋 선택 또는 커스텀 페르소나 정의. /user-test:setup [preset|custom] [count]"
version: 1.0.0
argument-hint: "[preset-name|custom] [persona-count]"
allowed-tools: ["Read", "Write", "Glob", "Bash", "Agent"]
---

# User Test Setup

페르소나 그룹을 구성한다. 결과는 `.user-test/personas.json`에 저장된다.

## 입력 파싱

`$ARGUMENTS`를 파싱한다:
- 첫 번째 인자: 프리셋 이름 또는 `custom`
- 두 번째 인자 (선택): 페르소나 수 (기본값: 프리셋 기본 수)
- 인자 없으면: 대화형으로 선택

## 실행 절차

### Step 1: 프리셋 목록 로드

플러그인의 `presets/` 디렉토리에서 사용 가능한 프리셋을 읽는다:

```
${CLAUDE_PLUGIN_ROOT}/presets/*.json
```

각 프리셋의 `name`과 `description`을 사용자에게 보여준다.

### Step 2: 프리셋 선택 또는 커스텀 구성

**프리셋 선택 시:**
- 해당 프리셋의 페르소나를 로드
- 사용자가 수를 지정하면 랜덤 또는 선택적으로 추출
- 사용자가 속성 수정을 원하면 개별 조정 가능

**커스텀 구성 시 (`custom`):**
- 사용자에게 다음 속성을 질문하며 페르소나를 하나씩 구성:

```
인구통계: 연령, 성별, 거주지역, 소득수준, 직업
행동/경험: 도메인 숙련도, IT활용능력, 경쟁사 경험, 현재 대안
심리/상황: 기술수용도, 동기(Pain Point), 사용 맥락
```

- 최소 2명, 최대 10명
- 각 페르소나에 고유 `id`와 `label` 부여

**혼합 구성:**
- 프리셋을 기반으로 일부 수정하거나 커스텀 페르소나를 추가할 수 있다

### Step 3: 저장

구성된 페르소나 그룹을 `.user-test/personas.json`에 저장한다:

```json
{
  "preset": "프리셋명 또는 custom",
  "created_at": "ISO 날짜",
  "personas": [/* 페르소나 배열 */]
}
```

### Step 4: 확인

구성된 그룹을 테이블 형태로 보여주고 확인을 받는다:

| ID | Label | 연령 | IT숙련도 | 기술수용도 | 핵심 동기 |
|----|-------|------|----------|------------|-----------|

> 다음 단계: `/user-test:start [target]`으로 이 그룹에 UT를 실행하세요.
