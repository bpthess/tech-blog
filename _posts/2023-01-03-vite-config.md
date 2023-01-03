---
layout: post
title: Vite 코드 스플리팅 & 청크 분리
categories: vite, build, chunk, code splitting
author: tyLee
date: "2023-01-03 09:00:00 +0900"
---

-- Vite는 Webpack이 아닌 rollup 기반의 ESBuild를 사용하기 때문에 별도의 Webpack 설정과는 조금은 다르게 설정 해주어야 한다. 앱을 빌드한 후 프로젝트 규모가 크면 클수록 자바스크립트 번들링 사이즈가 같이 커지기 때문에 페이지 로딩 시간에 영향을 준다. 그래서 각 경로의 구성 요소를 별도의 청크로 분리하고 경로를 방문 할 때 코드 스플리팅으로 필요한 데이터만 불러오게 하였다.

<br>
<br>

<img src="{{'/assets/img/vite/vite_logo2.png' | relative_url}}" style="margin: 0 auto;">

<br>

### 코드 스플리팅

코드 스플릿을 입힘으로써 렌더링 시에 필요한 데이터만 가져와 렌더링 시간 단축과 메모리 사용을 줄일 수 있다.

<br>
<br>

#### 기존 라우터 경로

router.tsx

```tsx
<Routes>
  <Route path="dashboard" element={<Dashboard />} />
  <Route path="/login" element={<Login />} />
  <Route path="/register" element={<Register />} />
</Routes>
```

<br>
<br>

#### 변경 라우터 경로

router.tsx 상단

lazy로 동적 import후 변수로 선언

```tsx
const Dashboard = lazy(() => import("@pages/dashboard"));
const Login = lazy(() => import("@pages/auth/Login"));
const Register = lazy(() => import("@pages/auth/Register"));
```

<br>

router.tsx 하단

path에 사용하고자 하는 URL & 쿼리 스트링을 넣고, element에 선언한 변수명을 적어준다.  
children이 있는 이유는 자식 컴포넌트를 여러개로 나누었다. 그 중에 한 부분을 가져왔기 때문이다.

```tsx
{
  children: [
    { path: "/dashboard", element: <Dashboard /> },
    { path: "/login", element: <Login /> },
    { path: "/register", element: <Register /> },
  ];
}
```

워낙 오픈된 문서들이 많기에 쉽게 구현할 수 있다.

<br>
<br>

#### 추가로 커스텀 경로

tsconfig.json

```json
    "paths": {
      "@assets/*": ["./assets/*"],
      "@pages/*": ["./pages/*"],
      "@apis/*": ["./apis/*"],
    }
```

<br>

vite.config.ts

find가 커스텀하고자 하는 경로이고, resolve(\_\_dirname)을 사용하여 실제 경로를 넣어주었다.

```ts
export default defineConfig(({ mode }: { mode: string }) => {
      ...
return {
        ...
  resolve: {
      alias: [
        { find: "@assets", replacement: resolve(__dirname, "src/assets") },
        { find: "@pages", replacement: resolve(__dirname, "src/pages") },
        { find: "@apis", replacement: resolve(__dirname, "src/apis") },
      ],
    },
}
}
```

<br>
<br>

### 청크 분리

vite.config.ts

청크라는 변수명을 빈 객체를 만들어, Object.keys와 forEach를 활용하여 객체를 열거 할 수 있는 배열을 반복으로 돌린 다음, includes에 받아오는 데이터 값들로 유효한 데이터값이면 배열로 재선언하여 프로퍼티를 반환하게 만든다. 사용하고자 하는 곳에 spread인 ...을 넣어 기존의 것을 상속받은 새로운 객체를 만들어 dependencies를 파라미터로 사용한다. devDependencies도 사용 가능하다. 이렇게 하면 dependencies와 devDependencies도 별도로 분리할 수 있다는 장점이 있다.

```ts
import { dependencies } from "./package.json";

export default defineConfig(({ mode }: { mode: string }) => {

  // 청크 분리
  function renderChunks(deps: Record<string, string>) {
    let chunks = {};
    Object.keys(deps).forEach((key) => {
      if (["react", "react-router-dom"].includes(key)) return;
      chunks[key] = [key];
    });
    return chunks;
  }

  return {
    build: {
      rollupOptions: {
        // 청크 사용, dependencies
        output: {
          vendor: ["react", "react-router-dom"],
          ...renderChunks(dependencies),
        },
      },
    },
  }
}
```

<br>
<br>

#### 추가로 환경변수

vite는 webpack이 아닌 rollup 기반의 esbuild를 사용하기 때문에 import문을 기본으로 사용해야 하지만,  
런타임 직후에 읽어질뿐더러 환경변수 자체를 불러오지 않기 때문에 별도의 커스텀 env 설정하였다.

```ts
export default defineConfig(({ mode }: { mode: string }) => {
  // process.env 커스텀
  process.env = { ...process.env, ...loadEnv(mode, process.cwd()) };
}
```

<br>
<br>
