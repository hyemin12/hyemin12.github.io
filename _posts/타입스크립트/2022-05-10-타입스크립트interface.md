---
title: 타입스크립트 - interface
date: 2022-05-10 18:24:00 +09:00
categories: ["javascript", "타입스크립트"]
tags: ["타입스크립트"]
toc: true
toc_sticky: true
---

### @interface

- 오브젝트의 모양을 특정할 때만 사용가능
- React에서 많이 사용
- type보다 객체지향형 프로그래밍과 유사

```js
// Type
type Player = {
  nickname: string,
  team: Team,
  health: Health
};
```

```js
// interface
interface Player {
  nickname: string;
  team: Team;
  health: Health;
}
```

- 각각 인터페이스를 만들어도, 타입스크립트가 하나로 합쳐줌
- Type는 불가능

```js
interface User {
  name: string;
}
interface User {
  lastName: string;
}
interface User {
  health: number;
}

const person: User = {
  name: "name",
  lastName: "lastname",
  health: 1
};
```

```js
// Error!
type User = {
  name: string
};
type User = {
  lastName: string
};
```
