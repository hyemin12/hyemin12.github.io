---
title: git commit 오류
date: 2022-11-07 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

```bash
Another git process seems to be running in this repository, e.g.
an editor opened by 'git commit'. Please make sure all processes
are terminated then try again. If it still fails, a git process
may have crashed in this repository earlier:
remove the file manually to continue.
```

### 문제

파일을 수정하고 덮어 쓴 파일에 오류가 생김

### 해결방법

`.git/index.lock` 파일을 삭제하면 됨

- 리눅스 환경

```sh
rm -f ./.git/index.lock
```

- 윈도우

```sh
// .git 경로일 때
del index.lock
```

[참고 블로그](https://blog.naver.com/PostView.nhn?blogId=hyejin2351&logNo=221484138813&redirect=Dlog&widgetTypeCall=true&directAccess=false)
