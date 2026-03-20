# User Test Plugin

Multi-agent User Testing 플러그인. 페르소나 에이전트들이 스펙/서비스/API를 테스트하고 피드백을 수합한다.

## Commands

| Command | Description |
|---------|-------------|
| `/user-test:setup` | 페르소나 그룹 구성 (프리셋 또는 커스텀) |
| `/user-test:start` | 구성된 그룹으로 UT 실행 → 피드백 보고서 생성 |
| `/user-test:enhance` | 보고서 기반으로 코드/스펙 개선 제안 |
| `/user-test:run` | setup → start → enhance 원스톱 실행 |

## UT Modes

테스트 대상에 따라 자동 판별:
- **spec** — 문서/스펙 파일을 읽고 리뷰 (Read, Glob, Grep)
- **web** — 실제 웹 서비스를 브라우저로 조작 (chrome MCP 필요)
- **api** — API 엔드포인트를 직접 호출 (Bash/curl)

## Persona State

`/user-test:setup`으로 구성한 페르소나 그룹은 `.user-test/personas.json`에 저장된다.
이후 `/user-test:start`가 이 파일을 읽어 에이전트를 spawn한다.

## Presets

프리셋은 플러그인의 `presets/` 디렉토리에 JSON으로 정의되어 있다.
커스텀 페르소나도 동일한 스키마로 `.user-test/personas.json`에 직접 작성 가능.
