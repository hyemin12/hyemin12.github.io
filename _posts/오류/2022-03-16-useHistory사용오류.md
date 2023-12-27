---
title: useHistory 사용 오류
date: 2022-03-16 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

```bash
export 'useHistory' (imported as 'useHistory') was not found in 'react-router-dom'
```

### 문제

프로젝트 만드는 과정에서 history hook을 사용하려고 했더니, 오류가 발생

### 이유

버전에 따라 명령어가 변경됨

### 해결방법

1. 6버전 미만 버전으로 다운그레이드하기
2. useNavigate 대체하기

(1번) v6 미만 버전에선 useHistory 명령어

```js
const history = useHistory();

history.push("/");
history.goback();
history.go(-2);
history.push(`/user/${user._id}`);
```

(2번) v6 이상 버전에서는 useNavigate

```js
const navigate = useNavigate();

navigate("/");
navigate(-1);
navigate(-2);
navigate(`/user/${user._id}`);
```

---

참고 블로그

- [Attempted-import-error-useHistory-is-not-exported-from-react-router-dom](https://velog.io/@gaheekim610/Attempted-import-error-useHistory-is-not-exported-from-react-router-dom)
- [React Router v6 업데이트 정리](https://velog.io/@ksmfou98/React-Router-v6-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8-%EC%A0%95%EB%A6%AC)
