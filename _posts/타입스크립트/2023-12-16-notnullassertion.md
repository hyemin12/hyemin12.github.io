---
title: 타입스크립트 - not-null assertion
date: 2023-12-16 18:24:00 +09:00
categories: ["javascript", "타입스크립트"]
tags: ["타입스크립트"]
---

### ! (not-null assertion)

null과 undefined가 아님을 주장하는 연산자  
param이 null이나 undefined일 수도 있으니 책임하에 사용해야한다.

[타입스크립트 공식 문서 - non-null-assertion](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#non-null-assertion-operator-postfix-)

```ts
function a(param: string | null | undefined) {
  param.slice(3);
  // 'param' is possibly 'null' or 'undefined'.(18049)
}
```

params에는 string뿐만 아니라 null과 undefined 타입이 들어올 수 있기때문에 `param.slice(3)`코드가 타입 오류가 발생한다.

이럴 때 null과 undefined가 아니라고 명시해 타입오류를 해결할 수 있다.

```ts
function a(param: string | null | undefined) {
  param!.slice(3);
}
```
