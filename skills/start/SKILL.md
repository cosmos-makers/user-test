---
name: start
description: "User Test 실행 — 구성된 페르소나 에이전트들이 대상을 테스트하고 피드백 보고서를 생성. /user-test:start [target]"
version: 1.0.0
argument-hint: "<spec-file|url|api-endpoint>"
allowed-tools: ["Read", "Write", "Glob", "Grep", "Bash", "Agent", "WebFetch"]
---

# User Test Start

구성된 페르소나 그룹으로 User Test를 실행하고 피드백 보고서를 생성한다.

## 입력 파싱

`$ARGUMENTS`를 파싱한다:
- 대상 (필수): 파일 경로, URL, 또는 API 엔드포인트
- 인자 없으면: 사용자에게 테스트 대상을 질문

## 사전 조건

`.user-test/personas.json`이 존재해야 한다. 없으면 `/user-test:setup`을 먼저 실행하라고 안내.

## 실행 절차

### Step 1: 테스트 대상 판별

대상 유형을 자동 판별한다:

| 입력 | 판별 기준 | 모드 |
|------|-----------|------|
| 로컬 파일 경로 (`.md`, `.txt`, `.pdf`, 코드 파일 등) | 파일 존재 확인 | `spec` |
| `http://` 또는 `https://` URL | URL 패턴 | `web` |
| API 스펙 파일 (`.yaml`, `.json` + openapi/swagger) | 파일 내용 검사 | `api` |
| 디렉토리 경로 | 디렉토리 존재 확인 | `spec` (전체 코드베이스 리뷰) |

사용자에게 판별 결과를 확인받는다.

### Step 2: 테스트 컨텍스트 준비

**spec 모드:**
- 대상 파일/디렉토리를 읽어서 핵심 내용을 요약
- 요약을 각 에이전트에게 전달할 컨텍스트로 준비

**web 모드:**
- URL 접근 가능 여부 확인 (WebFetch로 상태 체크)
- 페이지 구조/주요 기능 목록을 사전 파악
- 각 에이전트에게 테스트 시나리오를 분배

**api 모드:**
- API 스펙을 파싱하여 엔드포인트 목록 추출
- 각 에이전트에게 테스트할 엔드포인트 분배

### Step 3: 페르소나 에이전트 병렬 실행

`.user-test/personas.json`에서 페르소나를 로드하고, **각 페르소나당 하나의 Agent**를 spawn한다.

각 에이전트에게 전달하는 프롬프트:

```
너는 다음 페르소나로 이 서비스/기능을 테스트하는 사용자야.

## 너의 프로필
- 이름: {label}
- 나이: {age}세, {gender}, {region} 거주
- 직업: {occupation}, 소득: {income}
- IT 활용: {it_proficiency}
- 기술 수용: {tech_adoption}
- 현재 대안: {current_workaround}
- 사용 맥락: {usage_context}
- 핵심 동기: {motivation}

## 테스트 대상
{컨텍스트}

## 지시사항
1. 위 페르소나의 관점에서 이 서비스/기능을 실제로 사용해보거나 검토하라
2. 다음 관점에서 평가하라:
   - **첫인상** (3줄): 처음 접했을 때의 느낌
   - **사용성** (1-5점 + 근거): 내 수준에서 쉽게 사용할 수 있는가
   - **유용성** (1-5점 + 근거): 내 문제를 해결해주는가
   - **Pain Points**: 불편하거나 막히는 지점 (구체적으로)
   - **Delighters**: 기대 이상으로 좋았던 점
   - **개선 제안**: 이렇게 바뀌면 더 쓸 것 같다 (우선순위 포함)
   - **재사용 의향**: 다시 사용할 것인가? (예/아니오 + 이유)
3. 페르소나의 어투와 관점을 유지하라. 20대 개발자와 60대 은퇴자의 시선은 완전히 다르다.
4. 결과를 구조화된 JSON으로 반환하라.
```

**모드별 에이전트 도구:**
- `spec`: Read, Glob, Grep (파일을 직접 읽고 분석)
- `web`: mcp__claude-in-chrome__* 도구들 (실제 브라우저 조작). 불가능하면 WebFetch 폴백
- `api`: Bash (curl), WebFetch (API 호출)

에이전트는 **sonnet 모델**로 실행하여 비용 효율을 확보한다.
가능한 한 모든 에이전트를 **병렬로** spawn한다.

### Step 4: 피드백 수합 및 보고서 생성

모든 에이전트의 피드백을 수합하여 보고서를 작성한다.

보고서 구조 (`.user-test/report-{timestamp}.md`):

```markdown
# User Test Report
- 대상: {target}
- 모드: {mode}
- 일시: {datetime}
- 참여 페르소나: {count}명

## Executive Summary
{3줄 핵심 요약}

## 정량 평가
| 페르소나 | 사용성 | 유용성 | 재사용 의향 |
|----------|--------|--------|-------------|

## 주요 발견

### Pain Points (빈도순)
1. {문제} — 언급: {N}명 — 심각도: {상/중/하}

### Delighters
1. {좋은 점} — 언급: {N}명

### 페르소나별 핵심 인사이트
#### {persona.label}
> {첫인상 요약}
- 핵심 피드백: ...

## 개선 권고사항 (우선순위)
1. **[P0]** {즉시 수정} — 근거: ...
2. **[P1]** {다음 스프린트} — 근거: ...
3. **[P2]** {백로그} — 근거: ...

## 페르소나 세그먼트 분석
- **IT 숙련도별**: Heavy vs Light 사용자의 반응 차이
- **연령대별**: 세대간 인식 차이
- **기술수용도별**: 얼리어답터 vs 레이트 마조리티 패턴
```

### Step 5: 완료 안내

> 보고서가 `.user-test/report-{timestamp}.md`에 저장되었습니다.
> 다음 단계: `/user-test:enhance`로 이 보고서 기반 개선을 시작하세요.
