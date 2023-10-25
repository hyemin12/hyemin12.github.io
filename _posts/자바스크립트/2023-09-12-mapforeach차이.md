---
title: map(), forEach() 차이
date: 2023-09-12 18:24:00 +09:00
categories: ["javascript", "javascript"]
tags: ["javascript"]
---

- map() 과 forEach()는 모두 반복문을 사용할 때 많이 사용되는 메소드이다.

### map()

- 새로운 배열을 반환
- 배열 내 모든 요소 각각에 대해 주어진 함수(콜백)을 호출한 결과를 모아 새로운 배열을 반환
- [mozilla - map()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

```js
const array1 = [1, 4, 9, 16];

// array1의 각 요소에 콜백(x * 2)를 실행
const map1 = array1.map((x) => x * 2);

console.log(map1);
// Expected output: Array [2, 8, 18, 32]
```

### forEach()

- 리턴값을 보내지 않는다
- 원본 배열을 변경하지 않음
- 단순히 **반복문을 대체하기 위한 메소드**
- [mozilla - forEach()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)\

```js
const array1 = [1, 4, 9, 16];

array1.forEach((element) => console.log(element));

// Expected output: 1
// Expected output: 4
// Expected output: 9
// Expected output: 16
```

## 예시

### arr 배열의 각 원소에 3을 곱하는 코드

#### 1. forEach()

- 원본 배열을 변경하지 않기 때문에, 변경된 내용을 담기 위한 배열을 변수로 지정해줘야 함

```js
// forEach
const arr = [1, 2, 3, 4, 5];
const result = [];

arr.forEach((num) => {
  result.push(num * 3);
});

console.log(result); // [3, 6, 9, 12, 15]
```

#### 2. map()

- 원본 배열 각각 요소에 콜백 함수를 실행한 후의 새로운 배열을 반환

```js
// forEach
const arr = [1, 2, 3, 4, 5];
const result = arr.map((num) => num * 3);

console.log(result); // [3, 6, 9, 12, 15]
```
