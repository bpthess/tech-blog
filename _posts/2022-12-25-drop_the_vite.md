---
layout: post
title: Vite 주세요
categories: vite
author: tyLee
date: "2022-12-25 09:00:00 +0900"
---

-- 하루마다 변해가는 게 프론트엔드 생태계에서 요즘 뜨고 있는 개발 프레임워크가 VITE이다. 처음에 코딩애플의 VITE 영상을 보고 처음 접하게 되었다. 뭐 대충 요약하자면 리액트보다 번들링이 10배 빠르다고 한다. 핫모듈을 이용해 번들링 시간을 단축시킬 수 있지만, 프로젝트가 커질수록 핫모듈에도 시간이 늘어나고 있다. 이를 보완 하고자 하여 나온 게 VITE이다. 기본적으로 ES Modules를 사용하며, Rollup처럼 쉽게 설정할 수 있다. HMR 등 부가적인 기능 역시 기본적으로 지원한다.

<br>
<br>

<img src="{{'/assets/img/vite/vite_logo2.png' | relative_url}}" style="margin: 0 auto;">

<br>

### 목차

1. <a href="#none">VITE란?</a>
2. <a href="#none">셋팅</a>

<hr>
<br>

### VITE란?

> [[공식문서]](https://vitejs-kr.github.io/) native ES module 기반으로 개발환경을 제공

##### 이야기

[마이그레이션](https://engineering.ab180.co/stories/webpack-to-vite)

[시간 단축](https://darekkay.com/blog/create-react-app-to-vite/)

<br>
<br>

### 셋팅

##### SWC

[https://fe-developers.kakaoent.com/2022/220217-learn-babel-terser-swc/](https://fe-developers.kakaoent.com/2022/220217-learn-babel-terser-swc/)

[https://min9nim.vercel.app/2022-05-31-cra-with-swc/](https://min9nim.vercel.app/2022-05-31-cra-with-swc/)

<br>
<br>

##### 루트

<img src="{{'/assets/img/vite/vite_root.png' | relative_url}}" style="margin: 0 auto;">

<br>
<br>

<b>노드버전</b>

<img src="{{'/assets/img/vite/vite_node.png' | relative_url}}" style="margin: 0 auto;">

<br>
<br>

##### 실행

<img src="{{'/assets/img/vite/vite_scripts.png' | relative_url}}" style="margin: 0 auto;">

<br>
<br>

##### 절대경로

<img src="{{'/assets/img/vite/vite_alias.png' | relative_url}}" style="margin: 0 auto;">
* <a href='https://velog.io/@hunmok1027/vite-에서-절대경로-설정하기' target="_blank">vite-에서-절대경로-설정하기</a>

<br>
<br>

##### 환경변수

환경변수는 번들러에 따라 다르게 설정된다.

<img src="{{'/assets/img/vite/vite_env.png' | relative_url}}" style="margin: 0 auto;">

<img src="{{'/assets/img/vite/vite_process.png' | relative_url}}" style="margin: 0 auto;">

Vite에서는 process.env가 아닌 import.meta을 사용하여 설정해주어야 한다.

- <a href='https://velog.io/@katanazero86/Typescript-%EC%97%90%EC%84%9C-import.meta-%EC%82%AC%EC%9A%A9%EB%B2%95' target="_blank">import.meta 사용법</a>

<br>
<br>

tsconfig.json & tsconfig.node.json

<img src="{{'/assets/img/vite/vite_tsconfig.png' | relative_url}}" style="margin: 0 auto;">

module: commonjs → esnext

<br>
<br>

- commonjs, esnext 차이는 무엇인가?

<img src="{{'/assets/img/vite/vite_esnext.png' | relative_url}}" style="margin: 0 auto;">

- <a href='https://yceffort.kr/2020/08/commonjs-esmodules' target="_blank">CommonJS와 ES Modules은 왜 함께 할 수 없는가?</a>

<br>
<br>

<b></b>

##### API 설정

#axios interceptors & vite

```tsx
export default ({ mode }) => {
  process.env = { ...process.env, ...loadEnv(mode, process.cwd()) };

  return defineConfig({
    server: {
      host: true,
      proxy: {
        "/api": {
          target: process.env.VITE_API_URL,
        },
      },
    },
  });
};
```

- <a href='hhttp://daplus.net/node-js-process-cwd-와-__dirname의-차이점은-무엇입니까/' target="_blank">process.cwd ()와 \_\_dirname의 차이점</a>

<br>

apis/config/instance/index.ts

<img src="{{'/assets/img/vite/vite_axios.png' | relative_url}}" style="margin: 0 auto;">

<br>
<br>

컴파일 하면 env를 읽어들어오지 못해줘서 따로 설정을 함

설정함으로 컴파일 되는 시점에서 env 코드를 읽어 불러오게 함

<img src="{{'/assets/img/vite/vite_axios2.png' | relative_url}}" style="margin: 0 auto;">

<br>
<br>

```tsx
console.log(import.meta.env);
```

<img src="{{'/assets/img/vite/vite_log.png' | relative_url}}" style="width: 20%">

<br>
<br>

### 끝마치며

확실히 리액트보다 훨씬 빠르다. npm start만 해도 체감이 확 느껴질 정도이다. next에 이어 사용성이 급속도로 퍼져나갈 것으로 예상된다. 초기 세팅만 조금 다를 뿐 리액트와 크게 다를 게 없어 유지 보수면에서도 용이할 것으로 예상된다. 항상 무언가를 사용할 때 그것을 왜 사용해야 하는가?를 고민해야 한다. 하지만, 사람들이 자주 사용하는, 뜨고 있는, 트렌드인 이유가 분명히 있기에 레거시로 멈춰 서있는 것보다 그 흐름에 발맞춰 움직일 수 있어야 한다고 생각한다.

<br>

### 참조

> <https://engineering.ab180.co/stories/webpack-to-vite>  
> <https://darekkay.com/blog/create-react-app-to-vite/>  
> <https://vitejs-kr.github.io/guide/>
