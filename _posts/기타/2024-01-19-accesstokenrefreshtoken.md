---
title: access token과 refresh token
date: 2024-01-19 18:24:00 +09:00
categories: ["기타", "로그인"]
tags: []
toc: true
toc_sticky: true
---

서버는 stateless 하기때문에 사용자의 정보를 저장할 수 없다. 따라서 우리는 로그인을 유지하기 위해 token을 사용한다.

## 📖 Access token, refresh token의 개념

### #Access token

- 클라이언트가 서버에게 유효한 사용자임을 검증받기 위해 사용하는 토큰이다.
- 해커가 토큰을 탈취하여 사용할 수 없도록 보통은 짧은 만료 시간을 가진다. (2시간 이내 또는 30분 이내 등)

### #refresh token

- Access Token이 만료되었을 때 새로운 Access Token을 발급받기 위한 토큰이다.
- 보통 긴 만료 시간을 가진다. (12시간 이상)
- refresh token을 사용하여 access token을 재 발급 받기때문에 매번 로그인을 하지 않아도 된다.

## 📖 Access token과 refresh token를 사용하는 이유?

### Access Token과 Refresh Token을 사용하는 이유는 보안과 사용 편의성을 모두 고려하기 위함!

- Access Token만 사용하는 경우, 해커가 token을 탈취할 경우 정상적인 사용자라고 인식하기 때문에 해당 token의 만료 시간이 지나기 전 까지는 없앨 수 없기 때문이다.

- Access Token과 refresh token를 사용하면 중요한 access token의 만료시간을 짧게 설정하고, refresh token의 만료시간을 길게 설정하여 token이 탈취당했을 때 해커가 오랜 시간 사용하지 못하도록 할 수 있다.

#### Q. 그렇다면, access token의 만료 시간을 짧게 설정하면 되는거 아닌가요?

A. access token은 사용자가 로그인했을 때만 즉, id 또는 이메일과 비밀번호를 입력했을 때 발급되는 token이다. **짧은 access token을 사용할 경우 사용자가 매번 재 로그인을 해야하는 번거로움이 생긴다**. 또한 매번 로그인을 해야하기 때문에 인증 서버에 부담이 간다.

## 📖 Access token 만료, refresh token 유효할 때

### Access Token이 만료되어 서버에서 인증이 거부되면, 클라이언트는 그 때 Refresh Token을 서버에 보내어 새로운 Access Token을 발급받는다.

#### ✨token 재발급 과정

1. 클라이언트가 서버에 API요청을 하면, 서버는 클라이언트가 전송한 `access token`의 유효성을 확인
2. `access token`이 만료되었을 경우, 서버는 `401 Unauthorized`상태 코드와 함께 응답을 보냄
3. 클라이언트는 저장해둔 `refresh token`을 이용해 서버에 새로운 `access token`을 요청함
4. 서버는 클라이언트가 전송한 `refresh token`을 검증함
5. `refresh token`이 유효하다면 새로운 `access token`을 발급
6. 클라이언트는 새롭게 발급받은 access token으로 1번에서 실패했던 API를 다시 요청함

#### Q. 그렇다면, 클라이언트는 토큰을 어디에 저장해두나요?

- http-only 속성이 부여된 쿠키에 저장을 한다. http-only 속성이 부여된 쿠키는 자바스크립트 환경에서 접근할 수 없기 때문에 XSS, CSRF가 발생해도 토큰이 누출되지 않음.
- 일반 쿠키나 브라우저의 로컬스토리지는 자바스크립트가 자유롭게 접근할 수 있기때문에 권장하지 않는다.

## 📖 Access token 만료, refresh token 만료일 때

### Access token 만료되고, refresh token도 만료되거나 유효하지 않을 경우에는 사용자는 다시 로그인을 해야한다.

참고 사이트

- [리프레쉬 토큰이 필요한 이유는 무엇인가요?](https://okky.kr/questions/1007579)
- [JWT에서 Refresh Token은 왜 필요한가?](https://velog.io/@park2348190/JWT%EC%97%90%EC%84%9C-Refresh-Token%EC%9D%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80)
