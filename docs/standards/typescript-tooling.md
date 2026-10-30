# TypeScript 공통 도구 초기 설정

React 또는 Node/TypeScript를 선택한 Step에서 읽습니다. 모든 명령은 해당 앱의 `package.json`이 있는 폴더에서 실행합니다. 아래 버전은 기존 프로젝트에서 검증한 조합이며 최신 버전을 자동 선택하지 않습니다.

## 기존 환경 확인

```bash
node --version
npm --version
```

기본 Node 버전은 24이며 이 도구 조합은 Node 22.13+ 또는 24+를 사용합니다. 지원 버전이 없으면 개발 환경의 설치 방식으로 준비합니다. `package.json`, lockfile, ESLint/Prettier 설정, `tsconfig`와 기존 검사 명령을 먼저 읽습니다.

- 필요한 도구가 이미 선언되어 있고 lockfile이 맞으면 `npm ci`로 복원합니다. 버전·설정을 재작성하지 않습니다.
- 빈 앱 폴더에서 시작하는 경우 `npm init -y`로 package.json을 만듭니다.
- 도구가 누락된 경우에만 아래에서 필요한 패키지를 추가합니다. `--save-exact`로 버전을 기록하고 생성된 lockfile도 보관합니다.

## 공통 패키지가 없는 경우

```bash
npm install --save-dev --save-exact typescript@5.9.3 eslint@10.13.0 @eslint/js@10.0.1 typescript-eslint@8.72.1 globals@16.4.0 eslint-config-prettier@10.1.8 prettier@3.6.2
```

새 프로젝트는 `npm pkg set type=module`로 ESM을 사용합니다. 기존 CommonJS 프로젝트는 실행 구조를 먼저 확인하고 설정 파일의 확장자·문법을 맞춥니다. 아래 ESLint 예시는 ESM 기준이며 React/Node 문서에서 해당 설정만 가져옵니다.

## 포맷 설정

`.prettierrc.json`:

```json
{ "singleQuote": true, "semi": true, "trailingComma": "all", "printWidth": 100, "tabWidth": 2 }
```

`.prettierignore`:

```text
node_modules
dist
build
coverage
package-lock.json
*.tsbuildinfo
```

## 검사 명령

아래 항목을 기존 package.json의 `scripts`에 병합합니다. 단일 tsconfig 기준 예시이며 기존 프로젝트 참조 구조에서는 해당 구조의 타입 검사 명령을 유지합니다.

```json
{
  "lint": "eslint . --max-warnings 0",
  "lint:fix": "eslint . --fix",
  "format": "prettier --write .",
  "format:check": "prettier --check .",
  "typecheck": "tsc --noEmit"
}
```

스택별 설정을 추가한 뒤 `npm run lint`, `npm run format:check`, `npm run typecheck`를 실행합니다. 포맷이 다르면 수정 후 다시 확인합니다. 처음부터 모든 파일을 자동 수정하지 말고 기존 프로젝트의 변경 범위를 확인합니다.

타입 정보를 사용하는 ESLint 설정이므로 검사할 TS 파일은 tsconfig에 포함되어야 합니다. 별도 테스트 폴더가 있으면 해당 tsconfig와 타입 검사 명령도 맞춥니다. 오류가 있을 때만 관련 공식 문서 부분을 조회합니다. 설치·검사 결과는 RUNBOOK/REVIEW에 기록하며 비밀 값이나 불필요한 전체 로그를 붙이지 않습니다.
