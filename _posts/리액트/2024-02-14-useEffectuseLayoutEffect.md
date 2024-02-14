---
title: React useEffect vs useLayoutEffect
date: 2024-02-14 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

### 📘React에서의 render와 paint

- render : DOM을 구성하기 위해 각 엘리먼트의 속성을 계산하는 과정
- paint: 실제 스크린에 Layout을 표시하고 업데이트하는 과정

## useEffect

[React 공식문서 - useEffect](https://ko.react.dev/reference/react/useEffect)

### 📘useEffect 실행 단계

![useEffect 실행 단계-miro.medium.com](https://miro.medium.com/v2/resize:fit:640/format:webp/1*Q5DfWHYDNQdfnal-IvW05g.png)

#### component → Render → Paint → useEffect

useEffect는 컴포넌트들이 render와 paint된 후에 실행된다. **비동기적**으로 DOM이 그려지고난 후에 **상태 값에 따라 다시 렌더링**된다.  
따라서 useEffect 내부에 DOM에 영향을 주는 코드가 있다면 화면이 깜빡이게 된다.

### 📘문법

```js
useEffect(() => {
  // do side effects
  return () => /* cleanup */
}, [dependancy array]);
```

- 의존성 배열을 통해 어떤 상태 또는 props가 변경될 때 실행시킬지를 정함

### 📘useEffect 예1 - 빈 의존성 배열

현재 시간 구하기 (시간, 분, 초)

- 1초에 한번씩 `setTime()`실행하여 현재 시각을 가져오는 코드

```js
function Time() {
  const [time, setTime] = useState(getTime());

  const getTime = () => {
    const now = new Date();
    return `${now.getHours()}:${now.getMinutes()}:${now.getSeconds()} `;
  };

  useEffect(() => {
    const timer = setInterval(() => {
      setTime(getTime());
    }, 1000);
    return () => clearInterval(timer);
  }, []);

  return <h2>{time}</h2>;
}
```

- `useEffect`의 의존성 배열이 비어있기 때문에 처음에 마운트 될 때만 실행
- 컴포넌트가 언마운트될 때 `return () => clearInterval(timer);` 코드가 실행되어 타이머를 정지시킴

<p class="codepen" data-height="300" data-default-tab="js,result" data-slug-hash="mdoxMYZ" data-user="jexbagvl-the-reactor" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/jexbagvl-the-reactor/pen/mdoxMYZ">
  Untitled</a> by 혬 (<a href="https://codepen.io/jexbagvl-the-reactor">@jexbagvl-the-reactor</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

### 📘useEffect 예2 - 의존성 배열 활용

시작버튼을 누르면 타이머가 실행되고, 멈춤 버튼을 누르면 타이머가 종료되는 타이머

```js
function Timer() {
  const [running, setRunning] = useState(false);
  const [timer, setTimer] = useState(0);

  useEffect(() => {
    let timeId;
    if (running) {
      timeId = setInterval(() => {
        setTimer((prevTime) => prevTime + 1);
      }, 1000);
    }
    return () => clearInterval(timeId);
  }, [running]);
  return (
    <>
      <h2>{timer}초</h2>
      <button onClick={() => setRunning(true)}>시작</button>
      <button onClick={() => setRunning(false)}>멈춤</button>
    </>
  );
}
```

- `useEffect`의 의존성 배열에 running이 들어있기 때문에 처음 마운트 될 때와 running의 상태가 변경될 때마다 실행됨
- running이 true일 때에는 `setInterval()`로직이 실행
- running이 false일 때와 컴포넌트가 언마운트 될 때 `return () => clearInterval(timeId);` 코드가 실행되어 타이머가 종료됨

<p class="codepen" data-height="300" data-default-tab="js,result" data-slug-hash="XWGEepY" data-user="jexbagvl-the-reactor" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/jexbagvl-the-reactor/pen/XWGEepY">
  Untitled</a> by 혬 (<a href="https://codepen.io/jexbagvl-the-reactor">@jexbagvl-the-reactor</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## useLayoutEffect

[React 공식문서 - useLayoutEffect](https://ko.react.dev/reference/react/useLayoutEffect)

### 📘useLayoutEffect 실행 단계

![useLayoutEffect 실행 단계-miro.medium.com](https://miro.medium.com/v2/resize:fit:640/format:webp/1*ZmRLve6CMNAuaQ5EFUa-8g.png)

#### component → Render → useLayoutEffect → Paint

useEffect와 동일하지만, 실행시점이 다르다. useLayoutEffect는 **렌더링된 후 paint 전에 동기적**으로 실행된다.  
즉, 브라우저가 화면을 다시 그리기 전에 실행되기때문에 DOM을 조작하는 코드가 존재하더라도 사용자는 깜빡임을 보지 않는다.

애니메이션 구현같이 반응이 바로 나타나야하는 경우에 사용하는 것이 좋다.

### 📘문법

```js
useLayoutEffect(() => {
  // do side effects
  return () => /* cleanup */
}, [dependancy array]);
```

### 📘useLayoutEffect 예1

윈도우 리사이즈 감지

```js
import React, { useState, useLayoutEffect } from "react";

function WindowWidth() {
  // window.innerWidth을 초기값으로 세팅
  const [width, setWidth] = useState(window.innerWidth);

  useLayoutEffect(() => {
    const handleResize = () => {
      setWidth(window.innerWidth);
    };

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return (
    <div>
      <h2>Window Width: {width}px</h2>
    </div>
  );
}

export default WindowWidth;
```

- `useLayoutEffect`의 의존성 배열이 비어있기 때문에 처음에 마운트 될 때만 실행
- 컴포넌트가 언마운트될 때 `return () => window.removeEventListener("resize", handleResize)`가 실행되어 이벤트 리스너를 정리

<p class="codepen" data-height="300" data-default-tab="js,result" data-slug-hash="gOEZrmR" data-user="jexbagvl-the-reactor" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/jexbagvl-the-reactor/pen/gOEZrmR">
  Untitled</a> by 혬 (<a href="https://codepen.io/jexbagvl-the-reactor">@jexbagvl-the-reactor</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## 결론

`useEffect`와 `useLayoutEffect`는 비슷하다. "언제 실행되는지"만 다르다.

- `useEffect`는 render와 paint가 모두 실행되고 난 뒤에 실행
- `useLayoutEffect`는 paint가 실행되기 전에 실행

`useLayoutEffect`를 사용하면 성능이 저하될 수 있기때문에 `useEffect`를 사용해보고, 버그가 발생할 경우 `useLayoutEffect`를 사용하는 것이 좋다!

참고 사이트

- [리액트 공식문서](https://ko.react.dev/reference/react/useEffect)
- [What is the real difference between React useEffect and useLayoutEffect?](https://pubudu2013101.medium.com/what-is-the-real-difference-between-react-useeffect-and-uselayouteffect-51723096dc19)
- [useEffect와 useLayoutEffect의 차이](https://www.howdy-mj.me/react/useEffect-and-useLayoutEffect)
