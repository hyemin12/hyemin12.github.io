---
title: React Hook Flow
date: 2024-02-14 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

![https://raw.githubusercontent.com/donavon/hook-flow/master/hook-flow.png](https://raw.githubusercontent.com/donavon/hook-flow/master/hook-flow.png)

## ✨Mount

컴포넌트가 페이지에 처음 렌더되는 단계

### 1. Run lazy initializers

`useState`와 `useReducer`의 `lazy initializers`는 초기 상태 계산을 하는데 사용이 된다. 또한 처음 렌더링될 때 한번만 사용이 되고(mount 단계에서만 실행), 초기값으로 할당된다.

### 2. Render

작성된 JSX를 가상 DOM에 렌더링 한다.

### 3. React updates DOM

가상 DOM이 업데이트가 되면 실제 DOM을 업데이트 한다.

### 4. Run Layout Effects

`useLayoutEffect`의 내부 코드를 실행 한다.

### 5. Browser paints the screen

브라우저가 업데이트된 DOM을 화면에 표시한다.

### 6. Run Effects

`useEffect`의 내부 코드를 실행 한다.

## ✨Update

컴포넌트가 업데이트되는 단계이며, Mount 단계와 유사하다.

### ■ 컴포넌트 업데이트되는 이유

- 부모 컴포넌트의 재-렌더링
- 컴포넌트 상태 변경
- 컨텍스트 변경

### 1. Render

작성된 JSX를 가상 DOM에 렌더링 한다.

### 2. React updates DOM

가상 DOM이 업데이트가 되면 실제 DOM을 업데이트 한다.

### 3. cleanup Layout Effects

`useLayoutEffect`의 반환 코드를 실행한다.

### 4. Run Layout Effects

`useLayoutEffect`의 내부 코드를 실행 한다.

### 5. Browser paints the screen

브라우저가 업데이트된 DOM을 화면에 표시한다.

### 6. Cleanup Effects

`useEffect`의 반환 코드를 실행한다.

### 7. Run Effects

`useEffect`의 내부 코드를 실행 한다.

---

## ✨Unmount

컴포넌트가 종료되는 단계

### 1. Cleanup Layout Effects

`useLayoutEffect`의 반환 코드를 실행한다.

### 2. Cleanup Effects

`useEffect`의 반환 코드를 실행한다.

참고 사이트

- [The Lifecycle of React Hooks Component](https://blog.bhanuteja.dev/the-lifecycle-of-react-hooks-component)
