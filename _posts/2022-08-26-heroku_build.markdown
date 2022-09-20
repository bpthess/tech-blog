---
layout: post
title: Heroku로 Express 서버 배포하기
categories: heroku,build,server
author: tyLee
date: "2022-08-26 09:00:00 +0900"
---

<br>
-- 나는 Docker로 개발 환경을 설계하였다. 하지만 AWS로 Docker를 배포가 가능하다는 사실을 뒤늦게 알게 되었던 터라, AWS가 아닌 heroku로 서버를 배포할 수 있다는 정보로 무작정 heroku로 서버 배포를 택하였다. AWS 말고 작은 규모의 프로젝트는 배포를 서버는 heroku로 클라이언트는 netlify를 이용한다고 한다. 나도 어떤 상황일 때 적재적소 하게 써야 할지 모른 채 무작정 heroku를 이용하여 서버를 배포하였다. 각각 장단점이 무엇인지 아직 스스로 정의 내려지지 않았기 때문에 다음의 포스팅 주제로 각각의 장단점에 대해 다루어 보려고 한다.  

<br>
### 배포 삽질 과정

폴더 경로 구조는 이렇다.

```bash
app
|---backend
|---frontend
```

나는 Gibhub Pages(프론트) + heroku(백엔드)로 연동하여 배포하고자 했다.  
우선 heroku cli를 설치했다.

```bash
$ npm install -g heroku (cli 설치 명령어)
```

<br>
다음은 heroku에 로그인 해야한다.
```bash
$ heroku login
```

<br>
아무키나 누르면 새창이 나타나면서 로그인 하라고 한다.  
계정은 미리 회원가입을 한 상태여서 바로 로그인했다.
<img src="{{'/assets/img/heroku/heroku-login.png' | relative_url}}">

<br>
로그인 성공
<img src="{{'/assets/img/heroku/heroku-login_success.png' | relative_url}}" style="width: 100%">

그 다음 heroku에 프로젝트 이름을 만들었다.

```bash
$ heroku create ${PROJECT-NAME}
```

<br>
이제 <code>$ git init</code>으로 remote에 heroku를 추가했다.   
<code>$ git remote -v</code>로 잘 연결되었는지 확인도 해주었다.
<img src="{{'/assets/img/heroku/heroku-remote_v.png' | relative_url}}" style="width: 100%">

<br>
이제 커밋 & 푸쉬해보자.   
<code>$ git add .</code>   
<code>$ git commit -m "${commit message}"</code>   
<code>$ git push heroku master</code>

<br>
빌드업 진행 중 아래 코드가 나타나면서 빌드 실패가 됐다.
```bash
!     No default language could be detected for this app.
			HINT: This occurs when Heroku cannot detect the buildpack to use for this application automatically.
			See https://devcenter.heroku.com/articles/buildpacks
 !     Push failed
```

<br>
빌드팩을 설치하라고 한다.   
공식 문서에 나온 대로 아래 코드로 nodejs 빌드팩을 설치했다.
> 공식문서 <https://devcenter.heroku.com/articles/buildpacks>
```bash
heroku buildpacks:set heroku/nodejs
```

<br>
다시 한번 <code>$ git push heroku master</code>했더니 다른 오류가 나왔다.
```bash
Heroku was unable to automatically detect the type of app you're trying to deploy
```

<br>
찾아본 결과 루트 디렉토리에 Procfile 생성하라고 한다.
Procfile은 heroku에게 프로젝트를 실행하기 위한 명령어의 순서를 알려주는 용도이다.
앱을 업로드한 후 Heroku가 노드 js 웹 서버를 시작하는 방법과 같은 dynos 설정을 정의할 Heroku 파일이다.
```bash
web : node ${package에 있는 실행 명령어 or 루트디렉토리 index파일 = server.js 입력 }
```
필자는 CRA가 아닌 node로 사용했기 때문에 루트 디렉토리 <code>node ${index파일명.js}</code>으로 입력하였다.
그런데도 똑같은 에러가 났다.
열심히 구글링 했더니 띄어쓰기 버그가 있다고 한다. <code>web : node</code>
하지만 실패.

노드와 npm 버전이 다를 수도 있다는 의견이 나왔다.
그래서 직접적으로 버전을 설정해주었다.

```bash
  "engines": {
    "node": "16.15.0",
    "npm": "8.5.5"
  },
```

그래도 실패.

<br>
이쯤되면 구조나, 빌드방식이 잘못되었다는 판단이 들었다. 그러던 중 root 디렉토리에 package.json이 없어서 그런 것이었다는 걸 찾게 되었다.
server 폴더안에 frontend 부분을 포함시켜 다른 레포지토리로 분리 할 수 있었으나, 루트 디렉토리 안에 각각의 파일경로로 관리하고 싶었기 때문에 git subtree를 사용하면 분리하지 않을 수 있었다.
> <https://stackoverflow.com/questions/5977234/how-can-i-push-a-part-of-my-git-repo-to-heroku>
```bash
git subtree push --prefix web heroku master
```

<br>
드디어 서버 빌드에 성공하게 되었다.
<img src="{{'/assets/img/heroku/heroku-build_success.png' | relative_url}}" style="width: 100%">

<code>경로: https://${FROJECT_NAME}.herokuapp.com/$ {api경로}</code>  
<img src="{{'/assets/img/heroku/heroku-build_data.png' | relative_url}}">  
해당 데이터를 잘 받아들어오고 있다.  
이제 깃허브에 연동하기만 하면 되는데 역시나.. 쉽게 되는 것 하나 없다.

<br>
<img src="{{'/assets/img/heroku/heroku-github.png' | relative_url}}">   
깃허브를 선택하고 Deploy Branch 눌렀더니 아래와 같은 에러가 나왔다.  
```bash
ERROR: Application not supported by 'heroku/nodejs' buildpack
 !     
 !     The 'heroku/nodejs' buildpack is set on this application, but was
 !     unable to detect a Node.js codebase.
 !         
 !     A Node.js app on Heroku requires a 'package.json' at the root of
 !     the directory structure.
 !     
 !     If you are trying to deploy a Node.js application, ensure that this
 !     file is present at the top level directory. This directory has the
 !     following files:
 !     
 !     app/
 !     README.md
 !         
 !     If you are trying to deploy an application written in another
 !     language, you need to change the list of buildpacks set on your
 !     Heroku app using the 'heroku buildpacks' command.
```
추측되는 경우는 2가지다. 하나는 해당 디렉토리에 package.json 누락되어서다. 하지만 이건 subtree로 우회해결하였다.
다른 하나는 heroku가 빌드팩을 감지 못하고 있는 것이다. 여기까지 삽질까지도 꽤나 긴 시간이 걸렸다.
그래서 결국 포기하고 app > backend > frontend 경로로 변경했다.
```bash
app
|---backend
    |---frontend
```

이 방식은 실제로 헤로쿠에 배포할 때는 익스프레스 서버에 리액트 빌드 파일을 배포하여 사용할 것이다.
그래서 위에 삽질했던 방식과는 달리 backend의 package.json이 heroku 호출에 반응할 것이다.
이 경우 빌드를 하기 위해서는 별도 셋팅이 필요하다.

<br>
## 서버 동시 시작
클라이언트단과 서버단 통신 연결은 이미 한 상태이기 때문에 생략하고 두 개의 서버를 동시에 시작해야하는데 concurrently 라이브러리를 사용했다.
```bash
  "scripts": {
    "start": "node server.js",
    "heroku-postbuild": "cd frontend && npm install && npm run build",
    "dev": "concurrently \"npm run dev:server\" \"npm run dev:client\"",
    "dev:server": "node server.js",
    "dev:client": "npm start"
  },
```
설정을 맞추고 <code>npm run dev</code>을 실행했다.
이제 두 개의 서버가 동시에 실행되었다.

<br>
다음 frontend로 가서 빌드를 해보자.
하지만 빌드 이전에 또 Route 설정이 필요하다.
```bash
// 리액트 정적 파일 제공
app.use(express.static(path.join(__dirname, "frontend/build")));

// 라우트 설정
app.get("\*", (req, res) => {
res.sendFile(path.join(\_\_dirname + "/frontend/build/index.html"));
});

````

<code>ReferenceError: __dirname is not defined</code>에러가 나왔다.
Express에서 CommonJS에서 사용하던 __dirname 변수가 ES 모듈에서는 없기 때문에 발생하는 에러이다.
```bash
import path from 'path';
const __dirname = path.resolve();
````

해당 에러는 path 모듈로 루트 디렉토리를 지정하는 경로를 제공해줘서 해결해준다.

<br>
> - <https://node-js.tistory.com/entry/Nodejs-dirname-is-not-defined-%EC%97%90%EB%9F%AC>   
> - <http://daplus.net/node-js-__dirname%EC%9C%BC%EB%A1%9C-path-join-vs-path-resolve/>

다시 루트 디렉토리의 package.json에서 script 안에 추가했다.

```bash
  "scripts": {
    "heroku-postbuild": "cd frontend && npm install && npm run build",
  }
```

<br>
그리고 다시 빌드를 실행했다.   
<code>$ git add .</code>   
<code>$ git commit -m "${commit message}"</code>   
<code>$ git push heroku master</code>

<br>
기대도 안했다. 역시 에러가 났다.
<code>error: failed to push some refs to 'https://github.com/bpthess/e-commerce.git'></code>

<br>
헤로쿠에 어떤 버전의 nodejs 사용하고 있는지 알려줘야 한다고 한다.
배포 전 heroku가 지원하는 nodejs 버전도 잘 확인해야한다.
> <https://devcenter.heroku.com/articles/python-support>

<br>
하위 디렉토리 runtime 파일 생성
```bash
$ nodejs-v16.15.0
```

<br>
다시 빌드해보자.
<img src="{{'/assets/img/heroku/heroku-build_success_02.png' | relative_url}}" style="width: 100%">
빌드에 성공했다. 이제 되는 것인가?!

<br>
<img src="{{'/assets/img/heroku/heroku-build_web_error.png' | relative_url}}" style="width: 100%">   
오.. 역시..   
어떤 에러가 나오는지 알아보자..   
```bash
$ heroku logs --tail
```

<br>
해당 에러는 <https://intrepidgeeks.com/tutorial/story-of-errors-in-designing-react-vite-with-heroku>
링크에서 안내하는대로 시도해봐야겠다.
```bash
at=error code=H10 desc="App crashed" method=GET path="/" host=e-commerce0001.herokuapp.com
at=error code=H10 desc="App crashed" method=GET path="/favicon.ico" host=e-commerce0001.herokuapp.com
```
<img src="{{'/assets/img/heroku/heroku-logs_tail.png' | relative_url}}" style="width: 100%">
