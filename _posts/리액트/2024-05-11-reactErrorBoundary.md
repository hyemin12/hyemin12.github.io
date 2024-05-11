---
title: 리액트 - react-Error-Boundary with react-query
date: 2024-05-11 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ErrorBoundary

오류를 핸들링할 수 있는 컴포넌트이다. 오류가 발생한 컴포넌트 대신, fallback을 보여준다.

### fallback

"어떤 기능이 제대로 동작하지 않을 때, 이에 대처하는 기능 또는 동작을 말합니다."

[리액트 공식문서](https://ko.legacy.reactjs.org/docs/error-boundaries.html)를 보면 클래스 컴포넌트로 작성해야한다고 나와있다.

### 기본 사용 방법

오류를 관리할 컴포넌트를 ErrorBoundary로 감싼 다음, 오류가 발생했을 때 보여줄 fallback 컴포넌트를 지정합니다.

```js
<ErrorBoundary FallbackComponent={ErrorFallback}>
  <Router />
</ErrorBoundary>
```

### 리액트 쿼리를 사용할 때 오류 처리하기

throwOnError 옵션을 true 값으로 설정하면, 오류를 던져 ErrorBoundary가 오류를 처리할 수 있게 해준다.

```js
const todos = useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodos,
  throwOnError: true
});
```

여기서 `retry` 옵션을 사용하면 재시도 횟수를 지정할 수 있다. 기본값은 3번이다.
그리고 ErrorBoundary에 `onReset` 옵션을 작성해줘야한다.

`useQueryErrorResetBoundary`는 react-query에서 제공해주는 훅으로, 가장 최근에 발생한 오류를 reset하는 함수입니다.
[공식문서 - useQueryErrorResetBoundary](https://tanstack.com/query/v4/docs/react/reference/useQueryErrorResetBoundary)

```ts
function App() {
  const { reset } = useQueryErrorResetBoundary();
  return (
    <ErrorBoundary FallbackComponent={ErrorFallback} onReset={reset}>
      <Router />
    </ErrorBoundary>
  );
}
```

만약 한 페이지의 데이터를 가져오지 못한 상황인데, 모든 페이지가 보이지 않는다면 사용자 측면에서 불편함을 느낄 수 있기 때문에 각각 페이지에 ErrorBoundary를 적용해 데이터를 가져오지 못했을 때 해당 페이지만 오류 페이지를 보여주고 나머지 페이지에는 접근 가능하도록 구현할 수도 있다.

```ts
function Post() {
  const { reset } = useQueryErrorResetBoundary();
  return (
    <ErrorBoundary FallbackComponent={ErrorFallback} onReset={reset}>
      <MainContent />
    </ErrorBoundary>
  );
}
```

하단 코드는 오류 페이지에 접근한 유저가 접근 권한이 없으면 로그인 페이지로 이동하는 버튼을, 새로고침(재시도)를 하는 버튼을 보여주는 코드 예시이다. [출처](https://www.datoybi.com/error-handling-with-react-query/)

```tsx
function ErrorPage({ error, resetErrorBoundary }) {
  const { status } = error.response;
  const navigate = useNavigate();
  const isNotAuthorized = status === 401 || status === 403;

  const onClick = () => {
    if (isNotAuthorized) {
      navigate("/login");
    } else {
      resetErrorBoundary(); // 재시도
    }
  };
  return (
    <>
      <h4>오류가 발생했습니다.</h4>
      <p>어떠어떠한 오류가 발생했고, 이유가 무엇인지</p>
      <button type="button" onClick={onClick}>{isNotAuthorized ? '로그인' : '새로고침'}</button>
    </p>
  );
}
```

참고 사이트

- [https://velog.io/@ajm0718/React-Query-Error-Boundary](https://velog.io/@ajm0718/React-Query-Error-Boundary)
- [https://tanstack.com/query/v5/docs/framework/react/guides/mutations](https://tanstack.com/query/v5/docs/framework/react/guides/mutations)
