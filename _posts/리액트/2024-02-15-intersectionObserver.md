---
title: InterSection Observer API
date: 2024-02-15 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨InterSection Observer API

특정 요소가 뷰포트 내에서 얼마나 보이는지를 비동기적으로 감지하는 기능을 수행합니다.

### 📘 사용방법

#### 1. Observer 생성

`new IntersectionObserver`를 사용하여 Observer를 생성하고, 콜백함수와 옵션을 설정

```js
const observer = new IntersectionObserver();
```

#### 2. 관찰대상 설정 (target 설정)

observer의 `observer` 메소드를 사용하여 감시할 요소를 지정합니다. 요소가 화면에 나타나거나 사라질 때 콜백함수를 실행

```js
observer.observe();
```

#### 3. 콜백함수 정의

target이 화면에 나타나거나 사라질 때 실행시킬 콜백함수를 정의  
`target`과 `intersectionObserverEntry` 객체의 배열을 인자로 받음

```js
const observer = new IntersectionObserver(([entry]) => {
  // 실행 시킬 콜백 함수
});
```

#### 4. 가시성 데이터 설정 (옵션 설정)

```js
const observer = new IntersectionObserver(
  ([entry]) => {
    // 실행 시킬 콜백 함수
  },
  {
    // 가시성 데이터 설정
  }
);
```

```js
{
  root: 관찰 대상 요소의 부모 요소 지정,
  rootMargin: 관찰 대상이 뷰포트와 떨어져있는 거리를 지정 (css margin과 유사),
  threshold: 관찰 대상의 가시성 비율 지정, 0 ~ 1사이의 수로 지정
}
```

#### 5. Observer 해제

`disconnect()` 메소드를 사용해서 관찰을 해제

### 📘 사용 예

해당 엘리먼트가 관찰되면 애니메이션 효과 실행하기

#### ＃해당 엘리먼트 관찰 컴포넌트

- 특정 엘리먼트(targetRef)가 관찰되면 isVisible을 true로 변경

```ts
import React, { useEffect, useRef, useState } from "react";

const IntersectionObserverComponent = ({
  children
}: {
  children: React.ReactNode;
}) => {
  const [isVisible, setIsVisible] = useState(false);
  const targetRef = useRef(null);

  useEffect(() => {
    const targetCopyRef = targetRef.current;
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (!isVisible && entry.isIntersecting) {
          setIsVisible(true);
        }
      },
      {
        root: null,
        rootMargin: "0px",
        threshold: 0.5
      }
    );
    if (targetCopyRef) {
      observer.observe(targetCopyRef);
    }

    return () => {
      if (targetCopyRef) {
        observer.unobserve(targetCopyRef);
      }
    };
  }, []);
  return (
    <div style={{ width: "100%", height: "100%" }} ref={targetRef}>
      {isVisible && children}
    </div>
  );
};

export default IntersectionObserverComponent;
```

#### ＃애니메이션 컴포넌트

- direction으로 어느쪽으로 날아오는 효과를 사용할 것인지 설정
- $topPosition: 해당 엘리먼트의 높이 설정
- $delay: 애니메이션 딜레이 설정 (기본값 0)

```ts
import React from "react";
import { Animated } from "./AnimatedComponent.styles";

interface AnimatedComponentProps {
  children: React.ReactNode;
  direction: string;
  $topPosition: number;
  $delay?: number;
}

const AnimatedComponent = ({
  children,
  direction,
  $topPosition,
  $delay = 0
}: AnimatedComponentProps) => {
  return (
    <Animated $topPosition={$topPosition} direction={direction} $delay={$delay}>
      {children}
    </Animated>
  );
};

export default AnimatedComponent;
```

#### ＃적용

- `IntersectionObserverComponent`를 사용해서 해당 엘리먼트 관찰 여부 확인
- 관찰되어 isVisible이 true가 되어 children이 보여지도록 설정

```ts
// import 코드 생략...

const MainPromotion = () => {
  return (
    <BackgroundImg height="572px" image={bgImg}>
      <IntersectionObserverComponent>
        <AnimatedComponent direction="right" $topPosition={13}>
          <CoffeeImg>
            <img src={coffeeImg} alt="과테말라 까시 씨엘로 250g" />
          </CoffeeImg>
        </AnimatedComponent>

        <AnimatedComponent direction="left" $topPosition={32}>
          <TextWrapper>
            <img
              src={TextImg}
              alt="과테말라, 그중에서도 고지대로 품질 좋은 커피를 재배하는 안티구아 벨리에서 자라난 우아한 꽃향기 가득한 원두를 소개합니다."
            />
            <MoreInfoButton color="#6a501a" marginTop={1.5} />
          </TextWrapper>
        </AnimatedComponent>
      </IntersectionObserverComponent>
    </BackgroundImg>
  );
};

const CoffeeImg = styled.div`
  max-width: 290px;
  position: absolute;
  left: 30%;
`;
const TextWrapper = styled.div`
  max-width: 400px;
  position: absolute;
  left: 55%;
`;
export default MainPromotion;
```
