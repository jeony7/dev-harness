# 개인 개발 하네스

요구사항·설계·구현·검증의 작업 지침과 코드 규칙입니다.

## 사용 순서

[AGENTS.md](AGENTS.md)와 현재 단계의 지침을 읽도록 요청합니다. 기존 프로젝트에서는 문서·코드 구조를 먼저 확인하고 필요한 지침만 적용합니다.

1. **요구사항:** [build-pdr 지침](.agents/skills/build-pdr/SKILL.md)에 따라 `docs/PDR.md`에 문제·우선순위·수용 기준을 작성합니다.
2. **설계와 논의:** [write-adr 지침](.agents/skills/write-adr/SKILL.md)에 따라 `docs/ADR.md`에 설계와 여러 Step의 분할안을 작성하고 사용자와 논의합니다.
3. **구현:** [implement-feature 지침](.agents/skills/implement-feature/SKILL.md)에 따라 합의된 Step 중 지정한 범위만 구현·검증합니다.
4. **리뷰:** [review-pdr 지침](.agents/skills/review-pdr/SKILL.md)에 따라 `docs/REVIEW.md`에 AC별 결과와 실제 검증 근거를 기록합니다.

스킬 호출을 지원하면 단계명으로 호출하고, 그렇지 않으면 해당 파일을 읽도록 요청합니다. 세션·도구를 바꿀 때는 요구사항·결정·진행 상태와 관련 파일을 전달합니다.

```text
AGENTS.md와 .agents/skills/build-pdr/SKILL.md를 읽고
프로젝트 요구사항과 이해관계자 의견을 docs/PDR.md에 정리해줘.
그다음 .agents/skills/write-adr/SKILL.md를 읽고
설계와 Step 분할안을 docs/ADR.md에 제안해서 나와 논의해줘.
아직 구현은 시작하지 마.
```

```text
AGENTS.md와 .agents/skills/implement-feature/SKILL.md를 읽고
합의한 STEP-001만 구현하고 검증 결과를 기록해줘.
```

```text
AGENTS.md와 .agents/skills/review-pdr/SKILL.md를 읽고
전체 P0 수용 기준과 실제 결과물을 대조해줘.
```

## 문서의 역할

| 문서 | 기록할 내용 |
|---|---|
| [PDR](docs/PDR.md) | 요구사항의 기준. 출처·가정·우선순위·REQ/AC |
| [ADR](docs/ADR.md) | 설계·중요한 결정의 근거·Step 계획과 진행 결과 |
| [리뷰](docs/REVIEW.md) | AC별 체크리스트·실행한 검사·발견한 문제 |
| [실행·운영 안내](docs/RUNBOOK.md) | 실제 앱의 빌드·실행·재현 방법과 남은 한계 |

AC가 변경되면 관련 REVIEW 판정을 미검증으로 바꾸고 다시 확인합니다.

## 에이전트 협업

작업량·독립성·조율 비용을 보고 직접 수행하거나 필요한 부분만 위임합니다. 메인이 한쪽을 구현하고 다른 쪽만 위임할 수도 있습니다. 서버·프론트를 분담하면 **같은 Step의 API 계약 확정 → 구현 → 결과 수집 → 실제 연결 검증**으로 진행합니다. 메인은 공통 문서·설정과 최종 통합을 맡습니다.

분담은 ADR의 해당 Step에 기록하고 [에이전트 운영](docs/SUBAGENTS.md)을 따릅니다. 작은 수정·의존성이 높은 작업이나 위임할 수 없는 작업은 메인이 순차 수행합니다.

```text
AGENTS.md와 .agents/skills/implement-feature/SKILL.md를 읽고
합의한 STEP-001을 구현해줘.
ADR의 API·상태 계약을 기준으로 작업량에 맞게 직접 구현하거나 필요한 부분만 위임해줘.
관련 Step·AC·계약·담당 코드부터 읽고, 의존성이 확인되면 범위를 넓혀줘.
같은 조건의 유효한 검사 결과는 재사용하고, 미확인인 실제 연결을 검증해줘.
검증 결과를 ADR·REVIEW·RUNBOOK에 반영하고 이 Step 범위에서 마쳐줘.
```

하네스를 갱신하면 `AGENTS.md`와 현재 단계의 지침을 다시 읽도록 요청합니다.

## 코드 기본값

- 서버: Kotlin/Spring + Gradle Kotlin DSL + ktlint. `api`, `application/{service,domain,infra}`.
- 프론트: React + TypeScript + Vite + Tailwind CSS, ESLint + Prettier. 기능별 폴더.
- Node 서버: 필요한 경우에만 TypeScript + Express. Kotlin 서버와 중복 구성하지 않습니다.

상세 규칙과 선택 근거는 `docs/standards/`에서 필요한 것만 읽습니다. 매 작업마다 스택을 재검색하지 않습니다. 앱 코드는 프로젝트 환경과 합의한 Step에 맞춰 작성합니다.

선택한 기술의 초기 설정은 [Kotlin/Spring](docs/standards/kotlin-spring.md), [React](docs/standards/react.md), [Node 서버](docs/standards/node.md)에 있습니다. React/Node의 공통 lint·포맷 설정은 [TypeScript 도구](docs/standards/typescript-tooling.md)를 함께 읽습니다. 기존 설정을 확인한 뒤 필요한 도구만 앱 프로젝트에 설치합니다.

공개 저장소에는 재사용할 지침과 빈 문서 양식만 관리합니다. 프로젝트의 비공개 요구사항·고객 데이터·인증 정보는 포함하지 않습니다.
