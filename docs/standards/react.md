# React 고정 기본값

## 도구와 범위

- React 19 + TypeScript strict + Vite 7 + Tailwind CSS 4.
- 앱 프로젝트에서 npm과 커밋된 `package-lock.json`을 사용합니다. 새 패키지는 역할이 실제로 필요할 때만 추가합니다.
- ESLint 10 flat config, typescript-eslint recommended, React Hooks·Refresh 검사, Prettier 3.
- ESLint는 코드 품질, Prettier는 포맷을 담당합니다. `eslint-config-prettier`를 마지막에 적용해 충돌을 막습니다.
- 상태는 우선 `useState`/`useReducer`와 필요한 Context로 관리합니다. 화면별 서버 캐시·복잡한 폼·라우팅이 필요해질 때 TanStack Query / React Hook Form+Zod / React Router를 선택적으로 추가합니다.
- 복잡한 접근성 UI가 필요하면 shadcn/ui 같은 검증된 컴포넌트 도입을 검토합니다.
- Spring API와 연결하는 CSR 앱이 기본입니다. SSR/SSG가 실제 요구일 때만 프레임워크 선택을 다시 결정합니다. React의 모든 프로젝트에 Vite를 권장한다는 의미가 아닙니다.

## 초기 설정: React/TypeScript를 선택한 경우

### 기존 설정 확인

먼저 [TypeScript 공통 도구](typescript-tooling.md)의 환경 확인을 수행합니다. 기존 React/Vite 프로젝트가 있으면 현재 플러그인·tsconfig·CSS·scripts를 유지하고 누락된 설정만 추가합니다. 아래는 신규 앱의 최소 예시입니다.

### 필요한 패키지가 없는 경우

공통 도구와 함께 앱 폴더에 설치합니다.

```bash
npm install --save-exact react@19.1.1 react-dom@19.1.1
npm install --save-dev --save-exact vite@7.1.7 @vitejs/plugin-react@5.0.4 tailwindcss@4.1.14 @tailwindcss/vite@4.1.14 @types/react@19.1.16 @types/react-dom@19.1.9 @types/node@22.18.10 eslint-plugin-react-hooks@7.1.1 eslint-plugin-react-refresh@0.4.24
```

`eslint.config.js`:

```js
import js from '@eslint/js';
import prettier from 'eslint-config-prettier';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import globals from 'globals';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  { ignores: ['dist/**', 'node_modules/**'] },
  js.configs.recommended,
  {
    files: ['**/*.{ts,tsx}'],
    extends: [...tseslint.configs.recommendedTypeChecked],
    languageOptions: {
      globals: { ...globals.browser, ...globals.node },
      parserOptions: { projectService: true, tsconfigRootDir: import.meta.dirname },
    },
    plugins: { 'react-hooks': reactHooks, 'react-refresh': reactRefresh },
    rules: {
      ...reactHooks.configs.recommended.rules,
      'react-refresh/only-export-components': ['error', { allowConstantExport: true }],
      '@typescript-eslint/consistent-type-imports': 'error',
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    },
  },
  prettier,
);
```

단일 설정의 `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-jsx",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "verbatimModuleSyntax": true,
    "isolatedModules": true,
    "noEmit": true,
    "skipLibCheck": true,
    "types": ["vite/client", "node"],
    "baseUrl": ".",
    "paths": { "@/*": ["./src/*"] }
  },
  "include": ["src", "vite.config.ts"]
}
```

`vite.config.ts`에 React·Tailwind와 동일한 경로 별칭을 연결합니다. API 프록시 대상은 실제 서버 포트에 맞춥니다.

```ts
import tailwindcss from '@tailwindcss/vite';
import react from '@vitejs/plugin-react';
import { fileURLToPath, URL } from 'node:url';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: { alias: { '@': fileURLToPath(new URL('./src', import.meta.url)) } },
  server: { proxy: { '/api': 'http://127.0.0.1:8080' } },
});
```

`src/styles/global.css`에 `@import "tailwindcss";`를 넣고 앱 진입점에서 CSS를 import합니다. 앱의 `index.html`과 React 진입점을 연결한 뒤 공통 scripts에 `"dev": "vite"`, `"build": "tsc --noEmit && vite build"`를 병합합니다.

### 설정 검증

공통 lint·포맷·타입 검사와 `npm run build`를 실행합니다. `npm run dev`로 화면과 스타일·API 연결도 확인합니다. 외부 접근이 필요한 호스팅 환경에서는 바인딩 주소와 허용 호스트를 해당 환경에 맞게 설정합니다.

## 폴더

```text
src/
  app/                 앱 시작, 페이지 조합, provider·라우터 설정
  features/
    health/
      api/             이 기능의 API 호출
      components/      이 기능의 화면
      hooks/           재사용할 실제 필요가 있는 훅
      types.ts         기능 타입이 분리할 만큼 생겼을 때
  components/ui/       업무 지식이 없는 공용 UI
  lib/                 HTTP 클라이언트 등 공통 기반 코드
  styles/              전역 스타일·테마
```

기능별로 묶는 기본 구조입니다. `hooks`, `types`, `utils` 같은 빈 폴더를 미리 모두 만들지 않습니다. 폴더명은 kebab-case, React 컴포넌트 파일은 PascalCase, 일반 TS 파일은 kebab-case로 통일합니다.

- `app`에서 기능을 조합하고, 공용 UI/`lib`가 특정 `features`를 import하지 않게 합니다.
- 컴포넌트는 렌더링·사용자 이벤트를 맡습니다. API 요청과 응답 파싱은 기능의 `api` 및 공통 HTTP 클라이언트에 둡니다.
- `any` 대신 타입과 `unknown`을 사용합니다. 외부 데이터의 TypeScript 타입 단언만으로 런타임 검증을 했다고 주장하지 않습니다.
- 상태 변경은 불변 방식으로 하고 Hooks 규칙을 지킵니다. Effect 의존성을 임의로 비워 lint를 통과시키지 않습니다.
- 로딩·빈 결과·오류·중복 요청을 처리합니다. 버튼에는 의미 있는 이름, 입력에는 label, 키보드 포커스를 제공합니다.
- 요청 URL은 `/api` 같은 상대 경로를 기본으로 합니다. 개발에서는 Vite proxy, 통합 배포에서는 같은 origin을 사용합니다.
- 여러 URL을 사용하는 CSR 라우터를 도입하면 통합 서버에도 화면 경로의 index.html fallback을 구현하고 직접 URL 접근·새로고침을 확인합니다. 미등록 `/api`와 누락된 정적 파일을 화면 HTML로 바꾸지 않습니다.
- 비밀 값은 프론트 소스나 `VITE_*` 변수에 넣지 않습니다.

## 검사

앱 프로젝트에 `npm run lint`, `npm run format:check`, `npm run typecheck`, `npm run build` 명령을 설정하고 사용합니다. 포맷 자동 수정은 `npm run format`입니다.

핵심 사용자 흐름은 실제 브라우저로 확인합니다. 의미 있는 복잡한 컴포넌트 로직에는 Vitest/Testing Library를 추가할 수 있지만, 간단한 정적 화면에 테스트 틀을 의무적으로 추가하지 않습니다.
