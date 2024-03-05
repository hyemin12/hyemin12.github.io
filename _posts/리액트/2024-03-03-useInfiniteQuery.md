---
title: tanstack Query - useInfiniteQuery
date: 2024-03-03 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨`useInfiniteQuery`

[https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery](https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery)

## 1. 소개

useInfiniteQuery 훅은 useQuery처럼 서버 데이터를 가져오기 위해 사용되는 훅입니다. useQuery와 다른 점은 useInfiniteQuery는 **페이지네이션된 데이터를 가져오는데 사용**한다는 점입니다. 페이지네이션된 데이터를 가져오는데 사용되기 때문에 **무한 스크롤처럼 계속해서 데이터를 가져와야하는 경우**에 사용합니다.

## 1. useInfiniteQuery

```js
const {
  fetchNextPage,
  fetchPreviousPage,
  hasNextPage,
  hasPreviousPage,
  isFetchingNextPage,
  isFetchingPreviousPage,
  ...result
} = useInfiniteQuery({
  queryKey,
  queryFn: ({ pageParam }) => fetchPage(pageParam),
  initialPageParam: 1,
  ...options,
  getNextPageParam: (lastPage, allPages, lastPageParam, allPageParams) =>
    lastPage.nextCursor,
  getPreviousPageParam: (firstPage, allPages, firstPageParam, allPageParams) =>
    firstPage.prevCursor
});
```

## 3. `useInfiniteQuery` 옵션

### ＃queryKey

- 필수
- 쿼리에 사용할 queryKey
- queryKey가 변경되면 쿼리가 자동으로 업데이트 됨

### ＃queryFn

- 필수
- 쿼리가 데이터를 요청하는데 사용하는 함수

### ＃initialPageParam

- 필수
- 첫번째 페이지를 가져올 때 사용할 기본 페이지 매개변수

### ＃getNextPageParam

- 필수
- 다음 페이지의 매개변수를 결정하는데 사용
- 새 데이터가 수신되었을 때, `getNextPageParam`함수는 데이터의 마지막 페이지와 모든 페이지의 전체배열, 페이지의 매개변수 정보를 모두 받음
- 다음 페이지가 없음을 나타내려면 undefined 또는 null을 반환해야함

```js
getNextPageParam: (lastPage, allPages, lastPageParam, allPageParams) =>
  TPageParam | undefined | null;
```

### ＃getPreviousPageParam

- 이전 페이지의 매개변수를 결정하는데 사용
- 새 데이터가 수신되었을 때, `getPreviousPageParam`함수는 첫번째 페이지와 모든 페이지의 전체 배열, 페이지의 매개변수 정보를 모두 받음
- 다음 페이지가 없음을 나타내려면 undefined 또는 null을 반환해야함

```js
getPreviousPageParam: (firstPage, allPages, firstPageParam, allPageParams) =>
  firstPage.prevCursor;
```

### ＃maxPages

- 무한 쿼리 데이터에 저장할 최대 페이지 수
- 최대 페이지수에 도달하면, 새 페이지를 가져오면서 페이지 배열의 첫번째 또는 마지막 페이지가 제거됨

## 4. `useInfiniteQuery` 반환값

useQuery와 동일

### ＃data.pages

- 모든 페이지가 포함된 배열 형태

### ＃data.pageParams

- 데이터를 가져오기 위해 사용된 파라미터를 배열 형태

### ＃fetchNextPage, fetchPreviousPage

- 이전/다음 데이터를 가져오게 해줌

### ＃hasNextPage, hasPreviousPage

- 이전/다음 데이터가 있는지 확인할 수 있음

### ＃isFetchingNextPage, isFetchingPreviousPage

- 이전/다음 데이터를 가져오는 중인지 상태를 나타냄

## 5. 예시 코드

```js
import React from "react";
import { useInfiniteQuery } from "react-query";

const fetchPosts = async (page = 0) => {
  const response = await fetch(`https://api.example.com/posts?page=${page}`);
  if (!response.ok) {
    throw new Error("Failed to fetch posts");
  }
  return response.json();
};

const PostList = () => {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    error
  } = useInfiniteQuery({
    queryKey: "posts",
    queryFn: fetchPosts,
    initialPageParam: 1,
    getNextPageParam: (lastPage) => lastPage.nextPage
  });

  if (error) {
    return <div>Error: {error.message}</div>;
  }

  return (
    <div>
      {data.pages.map((page, pageIndex) => (
        <React.Fragment key={pageIndex}>
          {page.posts.map((post) => (
            <div key={post.id}>{post.title}</div>
          ))}
        </React.Fragment>
      ))}

      {hasNextPage && (
        <button onClick={() => fetchNextPage()} disabled={isFetchingNextPage}>
          {isFetchingNextPage ? "Loading more..." : "Load More"}
        </button>
      )}
      {isFetching && <div>Loading...</div>}
    </div>
  );
};

export default PostList;
```

- 다음페이지가 있다면, 다음페이지를 로드하는 버튼 조건부 렌더링
- 다음 페이지를 가져오는 중이라면 `Loading more...` 텍스트를, 아니라면 `Load More` 텍스트 렌더
- `Load More` 버튼을 클릭하면 다음 페이지를 가져오는 함수 실행시키기
- `isFetchingNextPage` 상태라면 `Loading more...`버튼을 클릭하지 못하도록 disabled 상태로 변경

```js
{
  hasNextPage && (
    <button onClick={() => fetchNextPage()} disabled={isFetchingNextPage}>
      {isFetchingNextPage ? "Loading more..." : "Load More"}
    </button>
  );
}
```

- 처음에 데이터를 가져오는 중이라면 Loading 화면 렌더시키기

```js
{
  isFetching && <div>Loading...</div>;
}
```

## 6. useInfiniteQuery에서 data 형식 바꾸기

useInfiniteQuery를 사용하면 data안에 바로 data가 응답되는 것이 아닌 pages 속성 안에 배열 형태로 응답받기 때문에, 별도로 가공을 해줘야한다.

```json
// data의 형태

{
  "data": {
    "pages": [
      {
        "content": ""
      },
      {
        "content": ""
      }
    ]
  }
}
```

- 변환

```ts
const computedData = data ? data.pages.flatMap((page) => page.lists) : [];
```

- 페이지네이션값 변환

```ts
const pagination = data ? data.pages[data?.pages.length - 1].pagination : {};
```
