---
title: useCallback()
date: 2022-10-05 18:24:00 +09:00
categories: ["리액트", "리액트"]
tags: ["최적화"]
---

## useCallback()

함수를 메모이제이션하기위해 사용하는 훅

### useMemo()와 useCallback()의 차이점

|            useMemo()            |        useCallback()         |
| :-----------------------------: | :--------------------------: |
| 특정 결괏값을 재 사용할 때 사용 | 특정 함수를 재사용할 때 사용 |

### ■ 문법

```js
const memoizedCallback = useCallback(function, deps);
```

- 첫번째 인자 : 실행할 함수
- 두번째 인자 : 변경될 값 (배열 형태의 함수 실행 조건) => 넘어온 함수를 넘어온 배열 내의 값이 변경될 때 까지 저장해놓고 재사용할 수 있게 해줌

### ■ 사용하는 이유

어떤 리액트 컴포넌트 함수 안에 함수가 선언되어있다면 해당 컴포넌트가 렌더링될 때 마다 새로운 함수가 생성됨  
→ 함수가 복잡하면 렌더 시간이 오래 걸려 성능이 떨어질 수도 있기때문

useCallback()을 사용하면 해당 컴포넌트가 렌더링되더라도 함수가 의존하는 값들이 바뀌지 않으면 기존 함수를 반환  
→ 성능 최적화

#### <span style="color:#f00">단순히 컴포넌트 내에서 함수를 반복해서 생성하지 않기 위해 useCallback을 사용하는 것은 큰 의미가 없거나 손해인 경우들도 있기때문에 잘 판단해서 사용해야함!</span>

### ■ 예시 1

```js
const add = () => x + y;

const add = useCallback(() => x + y, [x, y]);
```

#### ● useCallback 사용 전

컴포넌트 랜더링 될 때마다 실행

#### ● useCallback 사용

x값과 y값이 변경되면 새로운 함수가 실행되어 add 변수에 할당,  
값이 변경되지 않는다면 다음 렌더링 때 함수를 재 사용함

### ■ 예시 2 - useMemo와 함께 사용하기

![https://camo.githubusercontent.com/3b4b23dca0dd4ff4da7f2af53c2f7a796983c7a7e38cfe956f0fb4d976d12773/68747470733a2f2f696d67312e6461756d63646e2e6e65742f7468756d622f523132383078302f3f73636f64653d6d746973746f72793226666e616d653d6874747073253341253246253246626c6f672e6b616b616f63646e2e6e6574253246646e2532466c796d4350253246627472504b3170657132332532464970306132624f414b574b685638497052534b66504b253246696d672e6a7067](https://camo.githubusercontent.com/3b4b23dca0dd4ff4da7f2af53c2f7a796983c7a7e38cfe956f0fb4d976d12773/68747470733a2f2f696d67312e6461756d63646e2e6e65742f7468756d622f523132383078302f3f73636f64653d6d746973746f72793226666e616d653d6874747073253341253246253246626c6f672e6b616b616f63646e2e6e6574253246646e2532466c796d4350253246627472504b3170657132332532464970306132624f414b574b685638497052534b66504b253246696d672e6a7067)

처음 렌더 될 때 Light 컴포넌트 모두 렌더링 되고, 침실과 욕실을 클릭했을 때도 해당 컴포넌트만 렌더되는 것이 아닌, 모든 컴포넌트가 재 렌더링이 됨

toggle 함수 참조값이 컴포넌트가 렌더링될 때마다 모두 바뀌기 때문에 재 렌더링이 일어남.

#### ● useCallback() 사용해서 막기

처음 렌더 될 때 Light 컴포넌트 모두 렌더링 되고, 주방 욕실 클릭 시 해당 컴포넌트만 렌더됨

useCallback()을 이용해서 함수를 감싸고, 두번째 인자(변경조건)을 넣어서 재렌더링 막기  
두번째 인자가 변경될 때만 해당 함수가 새롭게 실행됨!

```js
import React, { useCallback, useState } from "react";
import Light from "./Light";

function AppCallback() {
  const [masterOn, setMasterOn] = useState(false);
  const [kitchenOn, setKitchenOn] = useState(false);
  const [bathOn, setBathOn] = useState(false);

  const toggleMaster = useCallback(() => {
    setMasterOn(!masterOn);
  }, [masterOn]);
  const toggleKitchen = useCallback(() => {
    setKitchenOn(!kitchenOn);
  }, [kitchenOn]);
  const toggleBath = useCallback(() => {
    setBathOn(!bathOn);
  }, [bathOn]);

  return (
    <section>
      <div>
        <Light room="침실" on={masterOn} toggle={toggleMaster} />
        <Light room="주방" on={kitchenOn} toggle={toggleKitchen} />
        <Light room="욕실" on={bathOn} toggle={toggleBath} />
      </div>
    </section>
  );
}
export default AppCallback;
```

참고 사이트

- [React.memo, useCallback 사용으로 렌더링 최적화 하기(feat.React-Native,Redux)](https://velog.io/@shin6403/React.memo-useCallback-%EC%82%AC%EC%9A%A9%EC%9C%BC%EB%A1%9C-%EB%A0%8C%EB%8D%94%EB%A7%81-%EC%B5%9C%EC%A0%81%ED%99%94-%ED%95%98%EA%B8%B0feat.React-NativeRedux#%EC%9D%B8%EB%9D%BC%EC%9D%B8-%ED%95%A8%EC%88%98%EB%A5%BC-%EB%84%98%EA%B2%A8%EC%A3%BC%EB%8A%94-%EA%B2%83%EC%9D%B4-%EC%95%84%EB%8B%8C-%ED%95%A8%EC%88%98%EB%A5%BC-%EB%B0%94%EB%A1%9C-%EB%84%98%EA%B2%A8%EC%A3%BC%EB%8A%94-%EA%B2%83%EC%9D%B4%EB%8B%A4)
