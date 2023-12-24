---
title: tailwind css prettier
date: 2023-12-06 18:24:00
categories: ["html/css", "css"]
tags: []
toc: true
toc_sticky: true
---

## tailwind css

[https://tailwindcss.com/blog/automatic-class-sorting-with-prettier](https://tailwindcss.com/blog/automatic-class-sorting-with-prettier)

tailwind css는 이미 정의되어 있는 스타일 값을 가져와서 사용하는 것이기때문에 빠르게 개발할 수 있고, 일관된 디자인을 사용하기 때문에 협업하는데 좋다.  
하지만 class에 모든 스타일 속성의 값이 들어있다보니 가독성이 떨어진다는 단점이 있었다.

```html
<div
  class="text-gray-700 shadow-md p-3 border-gray-300 ml-4 h-24 flex border-2"
></div>
```

tailwind css와 prettier가 만든 prettier-plugin-tailwindcss를 사용하면 좀 더 규칙성 있게 코드를 변환해주며 단점을 보완할 수 있다.  
규칙을 커스텀할 수 없다는 단점이 있다.

### 사용법

패키지 설치
`npm install -D prettier prettier-plugin-tailwindcss`

prettier 구성 파일에 해당 코드 추가해서 prettier가 tailwindcss를 관리하도록 설정하기

```js
// .prettierrc

{
  "plugins": ["prettier-plugin-tailwindcss"]
}
```
