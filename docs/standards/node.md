# Node 서버 고정 기본값

- 기본 Node 버전은 24이며, 실제 앱 도구와 호환되는 제공 환경의 버전을 우선합니다.
- TypeScript strict, Express 5, ESM, npm + lockfile.
- ESLint flat config + typescript-eslint recommended + Prettier. 프론트와 포맷·타입 규칙을 일치시킵니다.
- 개발은 tsx, 배포는 `tsc`로 빌드한 JS를 Node로 실행합니다.

## 초기 설정: Node 서버를 선택한 경우

### 기존 설정 확인

[TypeScript 공통 도구](typescript-tooling.md)에서 런타임·package.json·lockfile·기존 설정을 먼저 확인합니다. 프론트 빌드에 Node가 필요하다는 이유만으로 Express 서버를 추가하지 않습니다. 아래는 실제 Node 서버가 필요한 신규 앱의 예시입니다.

### 필요한 패키지가 없는 경우

공통 도구와 함께 서버 앱 폴더에 설치합니다.

```bash
npm install --save-exact express@5.1.0
npm install --save-dev --save-exact @types/express@5.0.3 @types/node@22.18.10 tsx@4.20.6
```

`eslint.config.js`:

```js
import js from '@eslint/js';
import prettier from 'eslint-config-prettier';
import globals from 'globals';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  { ignores: ['dist/**', 'node_modules/**'] },
  js.configs.recommended,
  {
    files: ['**/*.ts'],
    extends: [...tseslint.configs.recommendedTypeChecked],
    languageOptions: {
      globals: globals.node,
      parserOptions: { projectService: true, tsconfigRootDir: import.meta.dirname },
    },
    rules: {
      '@typescript-eslint/consistent-type-imports': 'error',
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    },
  },
  prettier,
);
```

`tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "verbatimModuleSyntax": true,
    "skipLibCheck": true,
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"]
}
```

ESM의 TS 상대 import에는 출력 파일 기준의 `.js` 확장자를 적습니다. 공통 scripts에 아래 항목을 병합합니다.

```json
{
  "dev": "tsx watch src/server.ts",
  "build": "tsc",
  "start": "node dist/server.js"
}
```

### 설정 검증

공통 lint·포맷·타입 검사와 `npm run build`를 실행합니다. 서버 진입점을 작성한 뒤 `npm run start`로 실제 HTTP 응답을 확인합니다. 테스트를 추가하면 실제 테스트 실행 명령을 `npm test`에 연결하고 테스트 파일의 타입 검사도 포함합니다.

```text
src/
  app.ts                Express 구성; listen은 하지 않아 테스트에서 재사용
  server.ts             환경 읽기와 서버 시작
  api/
    health.routes.ts    HTTP 라우트·검증·응답
  application/
    health/
      health-service.ts 서비스·도메인·저장소 구현을 기능별로 배치
  lib/                  설정 등 공통 기반 코드
```

라우트에 업무 로직·DB 코드를 몰아넣지 않습니다. API 경계에서 입력을 검증하고, 예외를 한 곳에서 응답 형식으로 바꿉니다. API 타입 단언은 실제 검증이 아닙니다. Kotlin 서버를 쓰는 프로젝트에 Node 서버를 추가하는 기본 구성은 아닙니다.

앱 프로젝트에 `npm run lint`, `npm run format:check`, `npm run typecheck`, `npm test`, `npm run build`를 설정하고 변경에 맞는 검사를 실행합니다.
