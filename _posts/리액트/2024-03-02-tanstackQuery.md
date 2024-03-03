---
title: tanstack Query
date: 2024-03-02 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨tanstack Query(React Query)

[공식 문서](https://tanstack.com/query/latest/docs/framework/react/overview)

> TanStack Query (FKA React Query) is often described as the missing data-fetching library for web applications, but in more technical terms, it makes fetching, caching, synchronizing and updating server state in your web applications a breeze.
>
> TanStack Query는 흔히 웹 애플리케이션을 위한 데이터 가져오기 라이브러리로 설명되지만, 기술적인 측면에서 보면 웹 애플리케이션에서 서버 상태를 쉽게 가져오기, 캐싱, 동기화 및 업데이트를 할 수 있게 해줍니다.
>
> -tanstack query 공식문서-

**서버의 데이터 가져오기/업데이트, 캐싱, 에러 처리 등을 쉽게 할 수 있도록 돕는 라이브러리**이며, 서버에서 가져온 데이터를 담는 객체(data), 에러가 발생했을 때 에러 정보를 담는 객체(error), 데이터 가져오기/업데이트 중임을 나타내기(isPending)등 각종 유틸 기능을 제공한다. 동일한 요청을 동시에 여러번 해도 한번만 요청을 보내기 때문에 비동기 작업을 효율적으로 처리해준다.

### 💡설치 방법

```
npm install @tanstack/react-query
```

```
yarn add @tanstack/react-query
```

### 💡작성 방법

#### 1. 새로운 queryClient를 생성

```js
import { QueryClient } from "@tanstack/react-query";

export const queryClient = new QueryClient();
```

#### 2. 새롭게 생성한 queryClient를 최상위 파일(App)에 등록하기

QueryClientProvider를 가져오고, client값으로 생성한 queryClient를 전달

```js
import { QueryClientProvider } from "@tanstack/react-query";

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  );
}
```

#### 3. `useQuery`사용해서 데이터 가져오기

```js
import { useQuery } from "@tanstack/react-query";

function Example() {
  const { isPending, error, data } = useQuery({
    queryKey: ["repoData"],
    queryFn: () =>
      fetch("https://api.github.com/repos/TanStack/query").then((res) =>
        res.json()
      )
  });

  // 데이터를 로드하는 중에 보여줄 내용
  if (isPending) return "Loading...";

  // 데이터를 가져오는데 실패했을 때 보여줄 내용
  if (error) return "An error has occurred: " + error.message;

  // 데이터를 성공적으로 가져왔을 때 보여줄 내용
  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{" "}
      <strong>✨ {data.stargazers_count}</strong>{" "}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  );
}
```

## ✨기본 개념

### 📕 `query`와 `mutation`

#### ■ query

- 서버에서 데이터를 가져오는 작업
- 주로 `useQuery`를 많이 사용
- HTTP 메소드의 GET 요청의 경우

#### ■ mutation

- 서버의 데이터를 변경하는 작업
- 주로 `useMutation`를 많이 사용
- HTTP 메소드의 PUT,POST,PATCH,DELETE 요청의 경우

### 📕 queryKey

[https://tanstack.com/query/latest/docs/framework/react/guides/query-keys](https://tanstack.com/query/latest/docs/framework/react/guides/query-keys)

- queryKey를 기반으로 쿼리 캐싱을 관리
- 단순한 문자열 배열, 복잡한 배열 모두 가능
- queryKey의 배열 순서로 동일한 쿼리인지 아닌지를 판단하기 때문에 순서를 신경써야 함
- queryKey는 queryFn에 대한 종속성 역할을 함 (queryKey에 종속변수를 추가하면 쿼리가 독립적으로 캐시되고, 변수가 변경될때마다 쿼리가 자동으로 데이터를 가져옴)

#### ■ 예시

```js
useQuery({ queryKey: ['todos'], ... })
useQuery({ queryKey: ['something', 'special'], ... })
```

#### ■ 예시 - 쿼리 순서

- staus, page 동일한 값이 queryKey값으로 설정되었지만, 순서가 다르기때문에 모두 다른 쿼리로 판단됨

```js
useQuery({ queryKey: ['todos', status, page], ... })
useQuery({ queryKey: ['todos', page, status], ...})
useQuery({ queryKey: ['todos', undefined, page, status], ...})
```

#### ■ 예시 - 종속 변수 추가

- queryKey에 todoId라는 종속 변수를 추가하여, todoId가 변경되면 새로운 데이터를 가져오도록 설정

```js
function Todos({ todoId }) {
  const result = useQuery({
    queryKey: ["todos", todoId],
    queryFn: () => fetchTodoById(todoId)
  });
}
```

### 📕 queryFn (query Function)

[https://tanstack.com/query/latest/docs/framework/react/guides/query-functions](https://tanstack.com/query/latest/docs/framework/react/guides/query-functions)

- 비동기 작업을 수행하는 함수
- 성공적으로 데이터를 가져오거나, 오류를 발생시킴
- 자바스크립트의 내장 함수인 `fetch`를 사용할 때는 직접 오류를 발생 시켜야 함
- `QueryFunctionContext`사용하여 queryKey를 참조하는 queryFn을 작성할 수 있음

#### ■ 예시

```js
// 정의해둔 함수 사용
useQuery({ queryKey: ["todos"], queryFn: fetchAllTodos });

// 익명 함수 사용
useQuery({ queryKey: ["todos", todoId], queryFn: () => fetchTodoById(todoId) });

// 비동기 함수 사용
useQuery({
  queryKey: ["todos", todoId],
  queryFn: async () => {
    const data = await fetchTodoById(todoId);
    return data;
  }
});

// 인자를 받는 함수 사용
useQuery({
  queryKey: ["todos", todoId],
  queryFn: ({ queryKey }) => fetchTodoById(queryKey[1])
});
```

#### ■ 예시 - 오류 처리

1. new Error 객체 던지기
2. Promise.reject 사용해서 Promise를 거부하고 새로운 오류 객체 반환하기

```js
const { error } = useQuery({
  queryKey: ["todos", todoId],
  queryFn: async () => {
    // new Error 객체 던지기
    if (somethingGoesWrong) {
      throw new Error("Oh no!");
    }

    // Promise.reject 사용해서 오류 처리하기
    if (somethingElseGoesWrong) {
      return Promise.reject(new Error("Oh no!"));
    }

    return data;
  }
});
```

#### ■ 예시 - QueryFunctionContext

```js
function Todos({ status, page }) {
  const result = useQuery({
    queryKey: ["todos", { status, page }],
    queryFn: fetchTodoList
  });
}

// Access the key, status and page variables in your query function!
function fetchTodoList({ queryKey }) {
  const [_key, { status, page }] = queryKey;
  return new Promise();
}
```

참고 사이트

- [[React] TanStack Query v4 (React Query)](https://beomy.github.io/tech/react/tanstack-query-v4/)
