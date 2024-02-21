---
title: 스타일드 컴포넌트에서의 GlobalStyle, theme
date: 2024-02-21 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨스타일드 컴포넌트에서의 GlobalStyle, theme

스타일드 컴포넌트는 CSS-in-JS의 대표적인 라이브러리이다.

### 📘 GlobalStyle

GlobalStyle에는 주로 전역 스타일을 작성

- 브라우저별 기본 스타일을 재정의하는 reset css같은 코드들을 작성
- 전체 애플리케이션에 적용되는 스타일을 작성 (글꼴, 배경색, 폰트 크기 등)

#### 💡 사용하는 이유?

- 브라우저 기본 스타일 초기화
- 일관된 스타일 적용
- 유지보수 용이

#### 💡 작성법

styled-components에서 제공하는 `createGlobalStyle`을 사용하여 정의

```ts
import { createGlobalStyle } from "styled-components";

interface Props {
  themeName: ThemeName;
}

export const GlobalStyle = createGlobalStyle<Props>`
  html {
    -moz-text-size-adjust: none;
    -webkit-text-size-adjust: none;
    text-size-adjust: none;
  }
  body{
    padding:0;
    margin:0;
    min-height: 100vh;
    line-height: 1.5;
  }
  body,
  h1,
  h2,
  h3,
  h4,
  p,
  figure,
  blockquote,
  dl,
  dd {
    margin-block-end: 0;
  }
  ...등
`;
```

#### 📘 theme

공통적으로 사용할 테마의 코드를 작성

- color
- font
- 간격 및 여백
- 스타일 변수 정의

#### 💡 사용하는 이유?

- UI,UX 일관성 유지
- 유지보수 용이
- 확장성
- 재사용성

#### 💡 작성법

styled-components에서 제공하는 `ThemeProvider`를 사용

```js
// theme.js

const breakPoint = "1180px";

const common = {
  boxShadow: "box-shadow: 2px 2px 5px rgba(150, 150, 150, 0.4)",
  primaryColor: "rgb(102, 126, 234)",
  darkColor: "#464555",
  dangerColor: "#f33253"
};
const flex = {
  row: (justify = "start") => `
  display:flex; 
  flex-direction: row;
  align-items: center;
  justify-content: ${justify}`,
  column: (justify = "start") => `
  display:flex; 
  flex-direction: column;
  align-items: start;
  justify-content: ${justify};`
};

const theme = {
  breakPoint,
  common,
  flex
};
export default theme;
```

#### `ThemeProvider` 사용해서 theme 적용

```js
// App.jsx
import { ThemeProvider } from "styled-components";
import theme from "@/styles/theme";
import Header from "@/components/Header";
import Footer from "@/components/Footer";

function App() {
  return (
    <div>
      <ThemeProvider theme={theme}>
        <Header />
        <div>메인 콘텐츠</div>
        <Footer />
      </ThemeProvider>
    </div>
  );
}
```

#### `theme` 사용하기 1 - 컴포넌트 파일에서 사용

```jsx
const Footer = () => {
  return (
    <FooterStyle>
      <p>copyright(c) 2024 hyemin</p>
    </FooterStyle>
  );
};

const FooterStyle = styled.footer`
  width: 100%;
  padding: 1em 0;
  background-color: ${({ theme }) => theme.common.darkColor};
  p {
    font-size: 0.8em;
    color: #ccc;
    text-align: center;
  }
`;
```

#### `theme` 사용하기 2 - styles.ts 파일에서 사용

```js
import styled from "styled-components";
import theme from "@/assets/css/theme";

export const ErrorMessage = styled.p`
  padding: 0.25em 0;
  color: ${theme.common.dangerColor};
  font-size: 0.8em;
  text-align: left;
`;
```
