---
title: tanstack Query - useQueries
date: 2024-03-02 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨`useQueries`

[https://tanstack.com/query/latest/docs/framework/react/reference/useQueries](https://tanstack.com/query/latest/docs/framework/react/reference/useQueries)

## 1. 소개

useQueries은 useQuery처럼 서버 데이터를 가져오기 위해 사용되는 훅입니다. useQuery와 다른 점은 useQueries는 **여러 데이터를 병렬**로 가져올 때 사용한다는 점입니다. **데이터의 반환순서는 입력 순서와 동일**합니다. (순서대로 결과값을 받음)

## 2. useQueries

```js
const ids = [1,2,3]
const results = useQueries({
  queries: ids.map(id => (
    { queryKey: ['post', id], queryFn: () => fetchPost(id), staleTime: Infinity },
  )),
})
```

- id가 1,2,3인 post를 가져오도록 요청
- results에는 id가 1,2,3로 요청된 결과값이 순서대로 담긴 배열이 들어있음

## 3.useQueries 옵션

### ＃queries

- 필수
- 여러 쿼리들을 작성

### ＃combine 옵션

- 여러개의 결과값을 하나로 결합할 때 사용
- 결과를 원하는 형태로 변경하여 반환할 수 있음

```js
const ids = [1,2,3]
const combinedQueries = useQueries({
  queries: ids.map(id => (
    { queryKey: ['post', id], queryFn: () => fetchPost(id) },
  )),
  combine: (results) => {
    return ({
      data: results.map(result => result.data),
      pending: results.some(result => result.isPending),
    })
  }
})
```

## 4.useQueries

### 반환값은 useQuery와 동일
