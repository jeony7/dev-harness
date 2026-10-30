# 기본값의 근거

확인일: 2026-10-30. 아래는 공식 도구 구성과 이 하네스의 선택 근거입니다. 전체 개발자의 선호도 순위나 유일한 표준을 주장하지 않습니다. 이 기본값을 매 작업마다 다시 검색할 필요는 없습니다.

| 선택 | 근거 | 이 하네스에서의 판단 |
|---|---|---|
| React + TS + Vite | [React 공식 빌드 도구 안내](https://react.dev/learn/build-a-react-app-from-scratch) | Spring API와 연결하는 CSR 앱에 적합한 기본값 |
| Tailwind Vite plugin | [Tailwind 공식 Vite 설치](https://tailwindcss.com/docs/installation/using-vite) | 별도 CSS 빌드 설정을 줄이고 컴포넌트 가까이 스타일 작성 |
| ESLint flat + typescript-eslint | [공식 빠른 시작](https://typescript-eslint.io/getting-started/) | TS 코드 품질과 React 플러그인을 한 설정에서 검사 |
| Prettier + eslint-config-prettier | [공식 통합 안내](https://prettier.io/docs/integrating-with-linters) | 포맷과 코드 품질 규칙의 충돌 방지 |
| 기능별 프론트 구조 | 이 하네스의 설계 선택 | 한 기능의 API·화면·훅을 가까이 두고 공용 UI를 구분; 단일 공식 React 폴더 표준은 아님 |
| Spring Gradle | [Spring Boot Gradle 공식 안내](https://docs.spring.io/spring-boot/3.5/gradle-plugin/getting-started.html) | 사용자 지정 빌드 도구인 Gradle을 고정 |
| Kotlin ktlint | [ktlint 규칙](https://pinterest.github.io/ktlint/1.5.0/rules/standard/) | 자동 수정 가능한 Kotlin 스타일 사용 |
| Node + Express | [Express 설치 안내](https://expressjs.com/en/starter/installing.html) | Node 대안이 필요할 때 단순한 HTTP 서버 구성 |
| 저장소 내 작업 지침 | 이 하네스의 설계 선택 | 요구사항·설계·구현·검증 지침을 프로젝트와 함께 버전 관리 |

새로운 라이브러리나 버전으로 바꾸는 경우 기존 lockfile을 보존하고 변경 이유·검사 결과를 ADR나 작업 기록에 남깁니다.
