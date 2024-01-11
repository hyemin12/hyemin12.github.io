---
title: for in vs for of
date: 2024-01-09 12:24:00 +09:00
categories: ["javascript", "자바스크립트"]
tags: []
toc: true
toc_sticky: true
---

## for...in 과 for...of의 차이

### for...of

[mdn 공식 문서 - for...of](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...of)

반복가능한 객체에 대해 반복하는 반복문

#### 반복가능한 객체

- array
- map
- set
- string
- arguments

#### 예제

```js
const array1 = ["a", "b", "c"];

for (const element of array1) {
  console.log(element);
}

// Expected output: "a"
// Expected output: "b"
// Expected output: "c"
```

### for...in

[mdn 공식 문서 - for...in](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...in)

열거 가능한 모든 속성에 대해 반복하는 반복문

#### 특징

for...in문에서는 break와 continue를 사용할 수 없음

#### 예제

```js
const object = { a: 1, b: 2, c: 3 };

for (const property in object) {
  console.log(`${property}: ${object[property]}`);
}

// Expected output:
// "a: 1"
// "b: 2"
// "c: 3"
```

#### ES6가 나오고 나서는 for...in 문은 잘 사용되지 않음

가독성이 더 좋고, 성능이 더 좋음

```js
// for...in 사용
for (const key in object) {
  if (object.hasOwnProperty(key)) {
    const value = object[key];
    console.log(value);
  }
}

// forEach 사용
Object.values(object).forEach((value) => {
  console.log(value);
});
```
