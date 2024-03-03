---
title: tanstack Query - useMutation
date: 2024-03-03 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨`useMutation`

[https://tanstack.com/query/latest/docs/framework/react/reference/useMutation](https://tanstack.com/query/latest/docs/framework/react/reference/useMutation)

## 1. 소개

useMutation 훅은 서버의 데이터를 변경하기 위해 사용되는 훅입니다. 이 훅은 **HTTP의 PUT, POST, PATCH, DELETE 요청**에서 사용됩니다. 서버의 데이터 업데이트가 성공하기 전에도 UI를 업데이트할 수 있습니다. 또한 서버와의 데이터 동기화를 신경쓰지 않고, **먼저 사용자에게 성공 UI를 보여준 후 요청의 결과에 따라 성공/실패 UI를 업데이트하는 경우**에 사용됩니다.

## 2. useMutation

```js
const {
  data,
  error,
  isError,
  isIdle,
  isPending,
  isPaused,
  isSuccess,
  failureCount,
  failureReason,
  mutate,
  mutateAsync,
  ...
} = useMutation({
  mutationFn,
  gcTime,
  mutationKey,
  networkMode,
  onError,
  onMutate,
  onSettled,
  onSuccess,
  retry,
  retryDelay,
  ...
});

mutate(variables, {
  onError, // 오류 발생 시 호출될 함수
  onSettled, // 작업 완료 시 호출될 함수
  onSuccess // 성공 시 호출될 함수
});
```

## 3. useMutation 옵션

### ＃mutationFn

- 필수
- 비동기 작업을 수행하는 함수

```js
const deleteTodo = useMutation({
  mutationFn: (id) => axios.delete(`/api/delete/${id}`)
});
```

### ＃gcTime

- 사용하지 않는 캐시 데이터가 메모리에 유지되는 시간
- 무한대로 설정하면 가비지 수집이 비활성화 됨

### ＃onMutate

- 작업이 호출되기 전 실행될 함수

### ＃onSuccess

- 작업이 성공되었을 때 실행할 함수

### ＃onError

- 작업에 오류가 발생했을 때 실행할 함수

### ＃onSettled

- 작업이 완료되었을 때 실행할 함수

### ＃retry

- 재시도 여부 또는 재시도 횟수

### ＃retryDelay

- 재시도 간격

## 4. useMutation 반환값

### ＃data

- 응답 데이터를 저장

### ＃error

- 오류 정보를 담고있는 객체

### ＃status

- 쿼리의 상태를 담고있는 객체

### ＃mutate, mutateAsync

- **useMutation을 사용하여 작성한 내용이 실제로 실행**될 수 있도록 trigger 역할을 하는 것
- mutate는 요청에 응답 받은 결과를 `onSuccess`, `onSettled`, `onError`를 사용하여 처리
- mutateAsync는 Promise를 반환하기때문에 `then` 또는 `async await`형태를 사용해야함

```js
const { mutate } = useMutation(deleteData);

const deleteFn = (id) => {
  mutate(id, {
    onSuccess: () => {
      // 삭제 성공 시
      console.log("데이터 삭제 성공");
    },
    onError: (error) => {
      // 삭제 실패 시
      console.error("데이터 삭제 오류:", error);
    },
    onSettled: () => {
      // 작업 완료 후
      console.log("데이터 삭제 완료");
    }
  });
};

// deleteFn를 실행하여 특정 id의 데이터를 삭제
deleteFn(123);
```

### ＃reset

- 초기화 함수

### ＃failureCount

- 요청 실패 횟수

### ＃failureReason

- 요청 실패 이유
