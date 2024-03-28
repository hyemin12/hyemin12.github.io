---
title: 리액트 hoc, 컴포넌트 래핑
date: 2024-03-28 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨리액트 hoc, 컴포넌트 래핑

HOC, 컴포넌트 래핑 방식 모두 코드를 재사용하고, 로직을 추상화할 때 사용합니다.

### ✅컴포넌트 래핑 방식 (Wrapper Component)

특정 컴포넌트를 공통 로직을 처리하는 Wrapper 컴포넌트로 감싸는 방식입니다.

새로운 컴포넌트는 추가적인 로직을 처리하거나,

#### 장점

- 코드가 상대적으로 간결하고 직관적
- 여러 페이지에 동일한 로직을 쉽게 적용할 수 있음

#### 단점

- 불필요한 렌더링이 발생할 수 있음 (상태가 변경되었을 때 모든 페이지의 wrapper 컴포넌트가 리렌더링 될 수도 있음)

```js
import React, { useEffect } from "react";

// 인증이 필요한 컴포넌트를 감싸는 Wrapper 컴포넌트
const WithAuthentication = ({ children }) => {
  useEffect(() => {
    // 인증 상태를 확인하고 필요에 따라 리다이렉션 등을 수행할 수 있음
  }, []);

  return <div className="authenticated-wrapper">{children}</div>;
};

const ProfilePage = () => {
  return (
    <WithAuthentication>
      <Profile />
    </WithAuthentication>
  );
};
```

### ✅고차 컴포넌트 (Higher Order Component, HOC)

**컴포넌트를 받아서 그 컴포넌트를 확장하는 새로운 컴포넌트를 반환**합니다.

인증 고차 컴포넌트의 경우에는 인증 로직을 담당하는 고차 컴포넌트를 각 페이지에 적용하여 인증 상태를 확인하고, 필요에 따라 리다이렉션을 수행합니다.

#### 장점

- 컴포넌트 랩핑방식보다 성능이 효율적임 (상태가 변경되었을 때 해당 페이지만 리렌더링됨)
- 컴포넌트 간 결합도를 낮출 수 있음

#### 단점

- 코드가 복잡해질 수도 있음
- 여러 곳에서 사용하는 경우 관리가 어려울 수 있음

```js
import React, { useEffect } from "react";

const withAuthentication = (WrappedComponent) => {
  const WithAuthentication = (props) => {
    useEffect(() => {
      // 인증 상태를 확인하고 필요에 따라 리다이렉션 등을 수행할 수 있음
    }, []);

    return <WrappedComponent {...props} />;
  };

  return WithAuthentication;
};

const ProfilePage = withAuthentication(Profile);
```

## ✅비교

### 래핑된 컴포넌트

- 다른 컴포넌트를 감싸는 역할
- 감싸진 컴포넌트에 추가적인 속성이나 기능을 제공하여 기능을 향상시킴
- 직접적으로 감싸진 컴포넌트와 상호작용하여 필요한 데이터나 기능을 전달
- 특정 컴포넌트에만 특정 기능을 추가하고 싶을 때 사용
- 특정 페이지나 UI 요소에 스타일이나 레이아웃을 추가하거나, 특정 컴포넌트에만 인증이 필요한 경우

### 고차 컴포넌트

- 컴포넌트를 인자로 받아 새로운 컴포넌트를 반환하는 함수
- 컴포넌트에 전달된 특정 데이터나 기능에 관심이 없으며, **단순히 컴포넌트를 향상**
- 여러 컴포넌트에서 동일한 기능을 공유하고 싶을 때 사용
- 여러 컴포넌트에서 인증이 필요하다면 HOC를 사용하여 인증 기능을 추상화하고 모든 컴포넌트에 적용

### hooks

- 공통 기능이 각 컴포넌트에서 쓰이기 전에 커스터마이징 되어야 하는 경우
  공통 기능이 앱 전반적으로 쓰이는 것이 아닌 하나나 혹은 몇개의 컴포넌트에서 요구되는 경우
  해당 기능이 기능을 쓰는 컴포넌트에게 여러 프로퍼티를 전달해야 하는 경우

참고 자료 및 출처

- [https://ko.legacy.reactjs.org/docs/higher-order-components.html#gatsby-focus-wrapper](https://ko.legacy.reactjs.org/docs/higher-order-components.html#gatsby-focus-wrapper)
- [https://stackoverflow.com/questions/36960675/difference-between-using-a-hoc-vs-component-wrapping](https://stackoverflow.com/questions/36960675/difference-between-using-a-hoc-vs-component-wrapping)
- [https://blog.hwahae.co.kr/all/tech/11631](https://blog.hwahae.co.kr/all/tech/11631)
- [https://patterns-dev-kr.github.io/design-patterns/hoc-pattern/](https://patterns-dev-kr.github.io/design-patterns/hoc-pattern/)
