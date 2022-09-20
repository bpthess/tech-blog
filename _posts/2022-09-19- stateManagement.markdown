---
layout: post
title: 상태관리를 해야 하는 이유
categories: stateManagement,redux
author: tyLee
date: "2022-09-19 09:00:00 +0900"
---

<br>
-- 리액트의 꽃이라고 할 수도 있는 상태관리에 대해 내가 공부하고 이해한 내용을 적고자 한다.   
리액트 뿐만아니라 뷰와 앵귤러에서도 리덕스를 사용할 수 있지만, 프레임워크 특성상 잘어울리는 라이브러리가 존재한다. 각각 Vuex, Ngrx정도로 알고 있다.
논외로 앵귤러 프레임워크는 이미 지원이 중단된지 오래된 상태이기 때문에 자세하게 알아보지는 않았다.  
지금은 내가 이해한 것들을 임시로 써놓은 상태이며, 추후 내용을 추가하고 다듬을 예정이다.
<br>

## 상태관리란?
https://reactjs.org/docs/faq-state.html에 의하면 
상태는 컴포넌트 내부에서 관리되며 어플리케이션의 렌더에 영향을 미치는 플레인 자바스크립트 객체이다. 즉 어플리케이션 화면에 영향을 끼치는 행위이다.   
이러한 상태를 관리해주는게 상태관리이고 
<br>

## 왜 상태관리를 해야 하는가?
1. 불필요한 데이터 호출 및 렌더링
2. 프롭 드릴링 문제
3. 컴포넌트간의 의존성 간소화(디커플링)
컴포넌트 추가, 수정, 삭제 시 기존의 컴포넌트를 모두 변경하게 된다. 기존의 컴포넌트는 서로 얽혀 있는 코드가 많기 때문이다  
상태관리는 의존된 컴포넌트간 객체 지향적으로 각 모듈을 독립적으로 사용할 수 있게 해준다.

<br>

## 상태관리가 없다면?
우리의 컴포넌트가(부품)이 여러개다. 그러면 우리가 신경써야 할 코드는 기하급수적으로 늘어난다.   
<br>

## 상태관리 패턴
FLEX 패턴
<br>

## 상태관리 툴 종류
Context API, Recoil, React Query, Mobx, Zustand
리덕스가 대표적이다. 하지만 비교적 어려운 사용법과 불편함으로 인해 간편한 라이브러리들이 나오고 있다.   
요즘은 Recoil + React Query조합이 좋다고 한다.
Context API + useReduer는 Redux와 같은 기능을 한다.
<br>

## 리덕스 사용기
리덕스의 핵심은 스토어이다. 스토어는 은행, 즉 정보가 저장되어 있는 저장소이다.   
스토어 안에는 state라는 실제 정보가 들어있다. state를 유지하는 것을 통해서 복잡성을 최소화한다. 
state를 절대 직접적으로 못건들인다. 항상 누군가가 도와주는게 있어야한다. 그 도와주는 과정은 이렇다.
action에서 dispatch로 데이터를 전달한 후 dispatch는 reducer를 호출할 때 2개의 값을 받아 전달한다.

1. 현재의 state값을 전달한다.
2. action하는 객체의 데이터를 전달한다.

reducer는 state를 입력값으로 받고, action을 참조해서 새로운 state를 만들어내서 리턴하는, 창조해서 가공하는 가공자이다.   
그러면 렌더링이 다시 호출된다. 

 
```tsx
const [state, setState] = useState('')

// state를 바꾸기 위해서는 action을 만들어줘야한다.
// 만든 액션을 디스패치에 제출하면 리듀서가 제출하게 되는데, 우리가 짤 리듀서를 state의 최종적인 값을 리턴해주면 된다.
const reducer(state, action) {
  if(action.type === 'create') {
    ...
    return (
      ...
    )
  }
}   
```

getState를 통해 값을 가져오고,
dispatch를 통해 값을 변경시키고,
subscribe는 값이 변경됬을 때 구동된 함수들을 등록해준다.
reducer를 통해서 스테이트값을 변경한다.

<br>

-- 이제 리덕스를 사용해보자
```bash
-> npm install --save redux react-redux redux-devtools-extension
```

```tsx
function reducer(state, action) {
  // 초기값(최초 한 번은 무조건 실행함)
  if(state === undefined) {
    return {...}
  }

  // 변경된 값
  let newState;
  if(action.type === 'CHABGE_TYPE') {
    newState = Object.assign({}, state, {...})
  }
}
 
// 전역변수
const store = Redux.createStore(reducer);
console.log(store.getState())

```

-- 스토어에 디스패치를 우리가 호출하게 되면 디스패치는 스토어를 생성할 때 제공되는 리듀서를 호출하게 약속되어 있다.   
그 전에 스테이트값과 전달된 액션의 값을 인자로 준다.
```tsx
store.dispatch({type: "SUCCESS"})

Object.assign({}, {...}, {...})
```

-- 리덕스는 단 하나의 스토어를 유지한다. 그 하나의 스토어는 리듀서라는 것을 통해서 가공되기 때문에 애플리케이션의 상태가 궁금하다 하면 리듀서를 확인하면 된다.
```tsx
console.log(action.type, action, state, newState, ...);
```
<br>

## 리덕스를 사용함으로써
스토어를 만들어서 상태들을 바꾸는게 핵심이다.   
리듀서라는 걸 만들어서 스토어에 주입시켜야한다.   
리덕스의 또 하나의 장점은 크롬 확장프로그램 Redux DevTool로 시간여행도구를 이용가능하다는 것이다.   
각각의 변화가 생겼을 때 어플리케이션을 외부적으로 어떤 상태인지 확인할 수 있을 뿐만아니라 동영상으로 어떤 시점에서 변화가 있었는지 확인 후 import해서 복원도 가능하다.

<br>

## 끝마치며
정리하자면 스토에 state값을 변경해준다. 액션의 값과 이전의 state값을 이용해서 새로운 state값을 리턴해주면 그 리턴된 값이 새로운 state값이 된다. 그 리턴하는 값은 원본을 바꾸는 것이 아니라 이전의 값을 복제한 결과를 리턴해야 우리가 redux를 사용하는 여러가지 효율들을 최대한으로 이용할 수 있다.   
action(상태가 바뀌는 것)을 store에 dispatch(통보)하면 된다. 그러면 reducer가 호출되서 최종적인 state값을 바꾸게 된다.
user action(상태 변경) -> dispatch -> reducer -> state
## 참조
https://jurgen-94.tistory.com/32  
https://dkrnfls.tistory.com/354  
https://mingule.tistory.com/74  
https://react.vlpt.us/redux/  
https://velog.io/@mokyoungg/Redux-react-redux-react-redux  
https://react.vlpt.us/redux/06-redux-devtools.html