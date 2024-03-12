---
title: styled-components (expectedts-styled-plugin(9999)
date: 2024-03-06 14:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

Book store 프로젝트에서 반응형 사이트로 만들기 위해 `@media screen`을 사용하고자 하였는데, 다음과 같은 오류가 발생하였다.

```
( expectedts-styled-plugin(9999)
```

- 문제가 되었던 코드

```tsx
const HeaderStyle = styled.header`
  // 기존 스타일 코드 생략
  @media screen and ${({ theme }) => theme.mediaQuery.mobile} {
    .logo {
      padding-left: 12px;
      img {
        width: 140px;
      }
    }
  }
`;
```

- theme에 작성한 스타일 값

```js
export type MediaQuery = 'mobile' | 'tablet' | 'desktop';

interface Theme {
  name: ThemeName;
  // 기존 값들

  mediaQuery: {
    [key in MediaQuery]: string;
  };
}
export const light: Theme = {
  name: "light",
  // 기존 값들
  mediaQuery: {
    mobile: "(max-width: 768px)",
    tablet: "(max-width: 1024px)",
    desktop: "(min-width: 1025px)"
  }
};
```

theme의 mediaQuery의 값들 안에 괄호를 쳐놓았기때문에 당연히 오류가 발생하지 않을 것이라고 생각하였는데, 오류가 발생하였다.

media query의 문법처럼 `${({ theme }) => theme.mediaQuery.mobile}` 코드를 괄호로 묶었더니 오류가 사라졌다.

```js
const HeaderStyle = styled.header`
  // 기존 스타일 값들

  @media screen and (${({ theme }) => theme.mediaQuery.mobile}) {
    .logo {
      padding-left: 12px;
      img {
        width: 140px;
      }
    }
    .search-wrapper {
      display: none;
    }
  }
`;
```

```js
export type MediaQuery = 'mobile' | 'tablet' | 'desktop';

interface Theme {
  name: ThemeName;
  // 기존 값들

  mediaQuery: {
    [key in MediaQuery]: string;
  };
}
export const light: Theme = {
  name: "light",
  // 기존 값들
  mediaQuery: {
    mobile: "max-width: 768px",
    tablet: "max-width: 1024px",
    desktop: "min-width: 1025px"
  }
};
```
