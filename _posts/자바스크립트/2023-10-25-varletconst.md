---
title: var, let, const
date: 2023-10-25 12:24:00 +09:00
categories: ["javascript", "javascript"]
tags: []
---

## var와 let의 차이

### 1. 스코프 (Scope)

#### ■ 스코프란?

코드가 변수에 접근할 수 있는 범위

#### ■ Function scope (함수 범위)

- 함수 내부에서만 접근이 가능
- block 밖에서 접근하더라도 변수가 선언된 함수 내부에서는 어디에서든지 접근이 가능
- **var 키워드**

#### @예시 - 함수 내부에서만 접근이 가능!

main() 함수 안에 선언되어 있는 x를 함수 밖에서 접근하려고 하면 오류가 발생함

```js
function main() {
  var x = "hi";

  console.log(x); // hi
}

main();
```

```js
function main() {
  var x = "hi";
}

console.log(x); // reference error 발생
```

#### @예시 - 변수를 선언한 함수 내부라면 어디서든 접근이 가능!

x를 선언한 main()함수 안에서는 if블록 안에서도 변수에 접근이 가능하고, if 블록밖에서도 접근이 가능함

```js
function main() {
  if (true) {
    var x = "hi";
    console.log(x);
  }
}

console.log(x); // hi
```

```js
function main() {
  if (true) {
    var x = "hi";
  }
  console.log(x);
}

main(); // hi
```

### ■ block scope (블록 범위)

- 변수가 선언된 블록 내부에서만 접근이 가능
- 더 엄격하게 코드를 작성할 수 있음
- **let키워드, const 키워드**

#### @예시 - 변수를 선언한 블록 내부에서만 접근 가능!

변수를 선언한 if 블록 안에서만 접근이 가능하고, if 블럭 밖에서 접근하려고 하면 오류가 발생함

```js
function main() {
  if (true) {
    let x = "hi";
    console.log(x); //hi
  }
}

main();
```

```js
function main() {
  if (true) {
    let x = "hi";
  }
  console.log(x); //reference error 발생
}

main();
```

#### @예시 - 블록안에서만 접근이 가능!

x라는 이름을 가지는 변수지만, 'hi'를 가지고 있는 x는 if 블록 안에서 선언이 되었기때문에 if 블럭 안에서만 접근이 가능함  
따라 if 블럭 밖에서 x로 접근하면 'hello'의 값이 출력되고, if 블럭 안에서 x로 접근하면 'hi'의 값이 출력됨

```js
function main() {
  let x = "hello";
  if (true) {
    let x = "hi";
    console.log(x); // 'hi';
  }
  console.log(x); // 'hello';
}

main();
```

#### @예시 - var였다면?

var였다면 변수가 선언된 main() 함수 내부에서는 어디서든 접근 할 수 있기때문에 나중에 선언된 변수로 값이 덮어씌어짐 (var x는 둘 다 같은 스코프를 갖는다.)  
따라 'hi'가 출력됨

```js
function main() {
  var x = "hello";
  if (true) {
    var x = "hi";
  }
  console.log(x); // 'hi';
}

main();
```

#### @예시 - for문에서의 let과 var의 차이

let을 사용하여 변수를 초기화하고 선언한 반복문에서는 for문 밖에서는 변수에 접근할 수 없음!

```js
function main() {
  for (let i = 0; i < 2; i++) {
    console.log(i);
    // 0
    // 1
  }
  console.log(i); //reference error 발생
}

main();
```

var를 사용하여 변수를 초기화하고 선언한 반복문에서는 for문이 종료되고 난 이후에도 접근이 가능하며, 마지막으로 실행된 값이 출력됨 (for문의 마지막 실행인 1에서 1을 더한 값)

```js
function main() {
  for (var i = 0; i < 2; i++) {
    console.log(i);
    // 0
    // 1
  }
  console.log(i); // 2
}

main();
```

### ■ global scope (전역 범위)

- 어느 함수에도 속하지 않은 최상위 함수 외부에 선언된 변수
- 함수나 블록으로 감싸있지 않아서 전역변수라고 볼 수 있음

```js
var aVar = "varHello";
var aLet = "letHello";
```

#### 만약 var로 전역 변수를 선언한다면?

var 키워드로 전역변수를 작성하면 window 속성으로 등록됨

```js
var aVar = "varHello"; //
var aLet = "letHello";

console.log(window); // window { aVar:'varHello',... }
```

### 2. 중복선언 (Variable redeclaration)

#### ■ var 키워드

- 같은 키워드를 중복해서 선언이 가능하고,
- 하단에 작성한 내용으로 덮어씌움

```js
var x = "안녕하세요";

// ...수많은 코드
var x = "hi";

console.log(x); // hi
```

#### ■ let 키워드

- 중복선언을 하지 못함
- 중복 선언했을 경우 오류가 발생함

```js
let x = "안녕하세요";

// ...수많은 코드
let x = "hi";

console.log(x); //Indentifier 'x' has already been declared
```

### 3. 호이스팅 (Hoisting)

코드를 실행하기 전에 함수, 변수, 클래스 등을 해당 범위 맨 최상단으로 끌려올려 지는 현상

#### ■ var 키워드

- var 키워드로 선언된 변수는 맨 위로 끌어올려질 때 자동으로 undefined 값으로 초기화가 됨

```js
console.log(num); // undefined

var num = 10;

console.log(num); // 10
```

#### ■ let 키워드

- TDZ (일시적 사각지대; Temporal Dead Zone)때문에 변수가 선언되기 전에는 변수에 접근하지 못하도록 막아줌

```js
console.log(num); // Cannot access 'num' before initialization

let num = 10;

console.log(num);
```

### const

- block scope
- 중복선언 불가
- 선언문 이전 접근 불가

- 상수를 선언 (다른 값으로 재할당 할 수 없음)

```js
const a = "hi";

a = "안녕하세요"; // Assignment to constant variable.
```

- 선언만 할 수 없음

```js
const a; // Missing initializer in const declaration.
```

- 객체의 속성은 변경 가능함 (새로운 객체를 할당하는 것이 아닌 이미 가지고 있는 객체의 속성을 변경하니까 가능한 것)

```js
const a ={
  x:1;
  y:2;
};

a.x = 3 // 객체

console.log(a) // { x:3, y:2 }
```

- 객체의 불변성을 유지하고 싶다면 `Object.freeze()`를 사용

```js
const a = Object.freeze({
  x: 1,
  y: 2
});
a.x = 3; // 객체

console.log(a); // { x:1, y:2 }
```

---

영상

[별코딩\_자바스크립트 - Var Let Const 차이 완벽정리 (스코프, 호이스팅)](https://www.youtube.com/watch?v=_zMVlKxmWHg)
