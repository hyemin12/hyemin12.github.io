---
title: animation-timeline
date: 2022-10-23 14:24:00 +09:00
categories: ["html/css", "css"]
tags: []
toc: true
toc_sticky: true
---

### animation-timeline()

- CSS만으로 스크롤 애니메이션을 즉시 구현할 수 있게 해주는 기능

- 메인 스레드에서 실행되지 않으므로 웹사이트가 느려지지 않는다는 장점을 가짐

- [mdn\_공식문서\_animation-timeline](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timeline)

### ■ 기존 방식의 CSS

스크롤을 하지 않아도, 애니메이션이 실행되기 때문에 자바스크립트로 스크롤해서 해당 요소를 만났을 때 애니메이션이 실행되도록 작성해야했음

```css
/* 애니메이션 */
@keyframes animateRect {
  from {
    opacity: 0;
    transform: scale(0.5);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.rect {
  width: 100%;
  height: 300px;
  background-color: skyblue;
  animation: animateRect 10s;
}
```

<p class="codepen" data-height="300" data-default-tab="css,result" data-slug-hash="mdvdapm" data-user="jexbagvl-the-reactor" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/jexbagvl-the-reactor/pen/mdvdapm">
  animation-timeline</a> by 혬 (<a href="https://codepen.io/jexbagvl-the-reactor">@jexbagvl-the-reactor</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

### ■ animation-timeline()

`animaition-duration` 속성을 작성하지 않고, 대신 `animation-timeline: scroll()`을 사용해 타임라인에 따라 스크롤 애니메이션이 작동됨

```css
/* 애니메이션 */
@keyframes animateRect {
  from {
    opacity: 0;
    transform: scale(0.5);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.rect {
  width: 100%;
  height: 300px;
  background-color: skyblue;
  animation: animateRect;
  animation-timeline: scroll();
}
```

<p class="codepen" data-height="300" data-default-tab="css,result" data-slug-hash="JjxjwZG" data-user="jexbagvl-the-reactor" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/jexbagvl-the-reactor/pen/JjxjwZG">
  animation-timeline2</a> by 혬 (<a href="https://codepen.io/jexbagvl-the-reactor">@jexbagvl-the-reactor</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

#### ● 브라우저 호환성

크롬, Edge에서 사용 가능
![호환성확인](https://github.com/hyemin12/hyemin12.github.io/assets/66300732/7368e455-e319-4e78-a4a8-58171bd289ad)
