---
title: npm ERR! code ENOENT(에러넘버 4058)
date: 2022-02-23 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

```bash
npm ERR! code ENOENT
npm ERR! syscall open
npm ERR! path C:\Users\user\Desktop\react-Blog/package.json
npm ERR! errno -4058
npm ERR! enoent ENOENT: no such file or directory, open 'C:\Users\user\Desktop\react-Blog\package.json'
npm ERR! enoent This is related to npm not being able to find a file.
npm ERR! enoent

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\user\AppData\Local\npm-cache\_logs\2022-02-13T13_03_01_683Z-debug-0.log
```

### 문제:

경로가 잘못 됨

### 해결방법:

프로젝트 폴더로 이동하여 명령어를 재 실행하니 정상작동 함
