---
title: InterSection Observer API
date: 2024-02-15 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨InterSection Observer API

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
