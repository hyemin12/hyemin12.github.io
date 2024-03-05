---
title: intersection Observer
date: 2024-03-05 12:24:00 +09:00
categories: ["javascript", "자바스크립트"]
tags: []
toc: true
toc_sticky: true
---

## intersection Observer

[https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)

## 1. 소개

요소가 뷰포트(화면)에 관찰되는지, 즉 관찰하는 요소가 지금 화면에 보이는지 아닌지를 구별하는 기능을 제공한다.

비동기적으로 실행되기때문에 메인 스레드에 영향을 주지 않으며 변화를 관찰할 수 있다. 즉, scroll같은 이벤트 기반의 요소를 관찰하며 발생하는 렌더링 성능, 이벤트 연속 호출과 같은 문제를 해결해준다.

## 2. 기본 문법

생성자 함수인 `new IntersectionObserver(callback, options);`를 사용해서 새롭게 관찰할 observer를 생성합니다.

```js
const observer = new IntersectionObserver(callback, options); // observer 초기화

observer.observer(element); // 관찰 대상 등록
```

- callback: 변화가 생기면 실행할 함수
- options: observer의 callback이 언제 호출될지를 제어하는 옵션

### ＃callback

관찰할 요소(타겟)이 등록되거나 가시성(관찰 대상이 화면에 보이는지 안보이는지)이 변화하면 실행할 함수입니다.  
callback은 `entries`,`observer` 2개의 인수를 가집니다.

```js
const observer = new IntersectionObserver((entries, observer) => {}, options);
observer.observer(element);
```

#### ■ entries

[IntersectionObserverEntry](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry)의 배열을 의미합니다. 읽기전용의 속성들을 제공합니다.

- `boundingClientRect`: 타겟의 사각형 정보
- `intersectionRect`: 타겟의 교차한 영역 정보 (가시 영역 정보)
- `intersectionRatio`: 타겟의 교차한 영역 비율 (0 ~ 1.0 사이의 숫자)
- `isIntersecting`: 타겟의 교차 상태 (타겟이 화면에 보여지는지 여부)
- `rootBounds`: 지정한 루트 요소의 사각형 정보
- `target`: 타겟 요소
- `time`: 변경이 발생한 시간 정보

##### 속성에 대한 자세한 정보는 [이곳](https://heropy.blog/2019/10/27/intersection-observer/)에서 확인하세요.

```js
const observer = new IntersectionObserver((entries, observer) => {
  entries.forEach((entry) => {
    console.log(entry);
  });
}, options);

observer.observer(element);
```

#### ■ observer

콜백이 실행되는 intersectionObserver를 의미합니다.  
delay, rootMargin, thresholds, observe, unobserve ...등의 값들을 가지고 있습니다.

```js
const observer = new IntersectionObserver((entries, observer) => {
  console.log(observer);
}, options);

observer.observer(element);
```

### ＃options

options를 사용하여 observer의 callback이 언제 호출될지를 제어할 수 있습니다.

- `root`: 타겟의 가시성을 확인할 때 사용되는 뷰포트
- `rootMargin`: root가 가진 바깥 여백
- `threshold`: 타겟의 호출 시점을 결정

#### ■ root

타겟의 가시성을 검사하기 위해 뷰포트대신 사용할 요소를 지정합니다.  
타겟의 조상요소이어야 하며, 지정하지 않을 경우 또는 null인 경우에는 브라우저의 뷰포트가 기본값으로 지정됩니다.  
safari, firefox for android에서는 지원되지 않는 옵션입니다. (출처: [mdn 공식문서 - 브라우저 호환성](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API#%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80_%ED%98%B8%ED%99%98%EC%84%B1))

```js
const observer = new IntersectionObserver(callback, {
  root: null; // 브라우저 뷰포트
})

const observer = new IntersectionObserver(callback, {
  root: document.getElementById('new-viewport')
})
```

#### ■ rootMargin

뷰포트의 바깥 여백을 의미합니다.  
뷰포트의 바깥 여백을 이용해 root의 범위를 확장하거나 축소할 수 있습니다.  
`px`또는 `%` 단위를 사용해야하며, css의 margin처럼 작성합니다.

```js
const observer = new IntersectionObserver(callback, {
  rootMargin: '10px 20px 30px 40px';
})

const observer = new IntersectionObserver(callback, {
  rootMargin: '10% 0';
})

const observer = new IntersectionObserver(callback, {
  rootMargin: '200px';
})
```

#### ■ threshold

타겟의 호출 시점을 결정하는 옵션입니다. 타겟의 가시성이 얼마나 필요한지, 즉 타겟이 얼마만큼 화면에 보여졌을 때 콜백을 실행할지 결정합니다.  
0.0에서 1.0 사이의 값으로 작성하며 배열 형태로 여러 값들을 작성해도 됩니다. 기본값은 0입니다.

```js
const observer = new IntersectionObserver(callback, {
  threshold: 0.3; // 요소가 화면에 30%만 보여졌을 때 콜백이 실행됨
})

const observer = new IntersectionObserver(callback, {
  threshold: [0.3, 1]; // 요소가 화면에 30% 또는 100% 보여졌을 때 콜백이 실행됨
})
```

## 3. intersectionObserver 메소드

### ＃observe()

관찰을 시작할 때 사용합니다.

```js
IntersectionObserver.observer(element);
```

```js
const observer = new IntersectionObserver(callback, options);

const div = document.querySelector("div");

observer.observer(div);
```

### ＃unobserve

특정 대상 요소에 대한 관찰을 종료할 때 사용합니다.

```js
IntersectionObserver.unobserve(element);
```

```js
const observer = new IntersectionObserver(callback, options);

const div = document.querySelector("div");

observer.observer(div); // 관찰 시작

observer.unobserve(div); // 관찰 종료
```

또는 callback 함수 안에서 사용하기도 합니다.

```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    // 관찰 대상이 보여지지 않은 경우 실행 X
    if (!entry.isIntersecting) {
      return;
    }

    // 관찰 대상이 보여질 때 실행할 코드 작성

    // 실행한 다음 관찰 종료
    observer.unobserve(entry.target);
  });
}, options);
```

### ＃disconnect

IntersectionObserver가 관찰하는 모든 요소에 대해 관찰을 종료할 때 사용합니다.

```js
IntersectionObserver.disconnect();
```

```js
const observer = new IntersectionObserver(callback, options);

observer.observer(document.querySelector("div"));
observer.observer(document.querySelector("li"));
observer.observer(document.querySelector("h1"));

observer.disconnect(); // observer가 관찰하고 있는 모든 요소에 대해 관찰 종료
```

## 4. 활용 예

### ＃무한 스크롤

페이지의 하단 또는 특정 요소가 관찰되면 새로운 콘텐츠를 동적으로 로드하는 무한 스크롤 기능을 구현할 때 사용합니다.

### ＃이미지 lazy loading (이미지 지연 로딩)

이미지 지연 로딩은 웹 페이지 내부의 실제 이미지들이 실제로 화면에 보여질 필요가 있을 때 로딩을 할 수 있도록하는 기법입니다.

한번에 많은 양의 이미지를 로드한다면 로딩 시간이 오래 걸리기때문에, 이미지 요소가 화면에 보여졌을 때 이미지를 로드하는 기능을 구현할 때 사용합니다.

### ＃스크롤 이벤트

사용자가 페이지를 스크롤할 때 특정 요소가 화면에 보여지고 보여지지 않을 때 이벤트를 추가할 때 사용합니다.  
예를 들면 스크롤을 내릴 때 특정 요소가 화면 밖에서 안으로 날아들어오는 애니메이션 효과를 구현할 수 있습니다.
또는 화면에 보여질 때 광고 영상을 재생시키고, 화면에서 보여지지 않을 때 광고 영상을 일시정지 시키는 기능을 구현할 수 있습니다.

## 5. 무한 스크롤 예시코드

```js
function List() {
  const targetRef = useRef(null); // 관찰할 대상을

  useEffect(() => {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          // 관찰 대상이 보여진다면 실행할 코드 작성하기
          loadMore();
        }
      });
      if (targetRef.current) {
        observer.observe(targetRef.current); // 관찰할 대상이 존재한다면 관찰 시작하기

        observer.unobserve(targetRef.current); // 관찰 종료하기
      }
    }, options);
  }, []);

  return (
    <div>
      <span ref={targetRef}>이게 보여지면 코드가 실행됩니다</span>
    </div>
  );
}
```

참고 사이트

- [mdn 공식문서](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)
- [Intersection Observer - 요소의 가시성 관찰](https://heropy.blog/2019/10/27/intersection-observer/)
