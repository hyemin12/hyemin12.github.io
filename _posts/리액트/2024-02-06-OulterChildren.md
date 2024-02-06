---
title: React Router의 Outlet vs React의 children
date: 2024-02-06 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## React Router의 Outlet vs React의 children

### 📘React Router의 Outlet

> An "Outlet" should be used in parent route elements to render their child route elements.  
> This allows nested UI to show up when child routes are rendered.  
> If the parent route matched exactly, it will render a child index route or nothing if there is no index route.
>
> 하위 경로 요소를 렌더링하려면 상위 경로 요소에서 "Outlet"을 사용해야 합니다.  
> 이를 통해 하위 경로가 렌더링될 때 중첩된 UI가 표시될 수 있습니다.  
> 상위 경로가 정확히 일치하면 하위 인덱스 경로가 렌더링되고, 인덱스 경로가 없으면 아무것도 렌더링되지 않습니다.

#### 즉, 페이지의 레이아웃을 처리하는데 사용

- 특정 라우터 구성 요소에서 하위 라우터의 컴포넌트들을 렌더링하는 역할을 함
- 페이지 간의 공통된 레이아웃을 정의하거나 중첩된 라우팅 구조에서 하위 라우트를 렌더할 때 주로 사용

#### ■ 사용예

`Layout`컴포넌트에서의 `Outlet`은 `/`경로에 대한 레이아웃과 `<About />`, `<Home />` 컴포넌트를 렌더링함

```js
// Layout.js
import { Outlet } from "react-router-dom";

const Layout = () => {
  return (
    <div>
      <header>Header</header>
      <Outlet />
      <footer>Footer</footer>
    </div>
  );
};
```

```js
// App.js
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import Layout from "./Layout";
import Home from "./Home";
import About from "./About";

const App = () => {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="about" element={<About />} />
        </Route>
      </Routes>
    </Router>
  );
};
```

#### ■ 사용예 - 내 코드

```js
import { Outlet } from "react-router-dom";
import { useAppSelector } from "@hooks/redux";
import { NavBar, SideBar } from ".";

const GenerLayout = () => {
  const { viewEditTagsModal, viewCreateNoteModal } = useAppSelector(
    (state) => state.modal
  );

  return (
    <>
      <SideBar />
      <div className="app__container">
        <NavBar />
        <Outlet />
      </div>
    </>
  );
};
```

### 📘React의 children

React에서 컴포넌트의 자식 요소들을 받아올 때 사용하는 prop

- 동적으로 변하는 자식요소를 다룰 때 주로 사용

#### ■ 사용예

`<ParentComponent/>`는 어떠한 자식요소들을 감쌀 수 있고, 동일한 스타일을 적용할 수 있음

```js
function ParentComponent({ children }) {
  return <div>{children}</div>;
}

export default function App() {
  return (
    <ParentComponent>
      <h4>HelloWorld!</h4>
      <p>children으로 prop 전달하기</p>
    </ParentComponent>
  );
}

export default function App2(){
   return (
    <ParentComponent>
      <h4>HelloWorld~~~!</h4>
    </ParentComponent>
  );
}
```

#### ■ 사용예 - 내 코드

```js
const Layout = ({
  children,
  $maxWidth = false
}: {
  children: React.ReactNode,
  $maxWidth?: boolean
}) => {
  const thisYear = new Date().getFullYear();

  return (
    <>
      <NavComponents />
      <Inner $maxWidth={$maxWidth}>{children}</Inner>
      <Footer>ⓒ {thisYear} starbuck Hyemin. All Rights Reserved. </Footer>
    </>
  );
};
```

### 📘children과 outlet의 차이

- `children`은 재사용 가능한 컴포넌트에서 자식 요소를 다루기 위한 요소
- `outlet`은 라우팅과 관련이 있고, 페이지 레이아웃을 구성하는데 사용

## ✨결론

Layout을 구성할 때 `children`과 `outlet`을 사용하는 것은 프로젝트의 구조마다 다르고, 개발자 스타일에 따라 다르다!

### ■ outlet

- 여러 페이지에서 공통적으로 사용되는 레이아웃을 구성할 때 주로 사용
- 중첩된 라우팅을 사용할 때 매우 유용
- 라우트 간 전환시 레이아웃 변화를 자연스럽게 해줌

### ■ children

- 레이아웃을 렌더링하는 컴포넌트에 대한 단순한 접근방식
- 작은 규모의 프로젝트거나 간단한 페이지 구조에서 사용하기 좋음

참고 사이트

- [reactrouter 공식 문서 - outlet](https://reactrouter.com/en/main/components/outlet)
- [react 공식 문서 - passing-jsx-as-children](https://ko.react.dev/learn/passing-props-to-a-component#passing-jsx-as-children)
