---
title: axios intercept를 사용해서 snakecase camelcase로 바꾸기
date: 2024-04-20 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

`axios`, `camelcase-keys`라이브러리를 사용해서 snakecase로 전달받은 속성 camelcase로 바꾸기

axios를 사용하여 응답(response)받은 데이터를 camelcase로 변경하고, 변경한 데이터를 return하는 방식으로 작성

```js
import camelcaseKeys from "camelcase-keys";

axios.interceptors.response.use(
  (response) => {
    if (response.data) {
      response.data = camelcaseKeys(response.data);
    }
    return response;
  },
  (error) => {
    return Promise.reject(error);
  }
);
```
