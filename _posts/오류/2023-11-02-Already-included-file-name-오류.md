---
title: Already-included-file-name-오류
date: 2023-09-21 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류", "클론유튜브"]
toc: true
toc_sticky: true
---

자바스크립트로 제작했던 포케몬 앱을 타입스크립트로 바꾸는 작업 중에 발생한 오류

svg 컴포넌트들의 className의 타입을 정의한 파일명을 `classNameTypes` 에서 `ClassNameTypes`로 파일 변경했더니

```
Already included file name
'../types/classNameTypes' differs from file name '../types/ClassNameTypes' only in casing.
The File is in the program because:
```

라는 오류가 발생하였다.

### 발생 원인

> Force Consistent Casing In File Names - forceConsistentCasingInFileNames
>
> TypeScript follows the case sensitivity rules of the file system it’s running on. This can be problematic if some developers are working in a case-sensitive file system and others aren’t. If a file attempts to import fileManager.ts by specifying ./FileManager.ts the file will be found in a case-insensitive file system, but not on a case-sensitive file system.
>
> When this option is set, TypeScript will issue an error if a program tries to include a file by a casing different from the casing on disk.

컴퓨터가 파일명의 대소문자를 구분하지 않도록 설정해 발생한 오류  
`ts.config.json` 파일 중 `forceConsistentCasingInFileNames`의 값이 true로 설정되어 있기 때문

### 해결

`forceConsistentCasingInFileNames`값을 false로 변경!

```js
// ts.config.json

{
  "compilerOptions": {
    ...
    "forceConsistentCasingInFileNames": false,
    ...
  },
}
```

---

참고사이트

- [[Error Log] TypeScript 파일명 변경 시 Already included file name 오류](https://velog.io/@sunohvoiin/Error-Log-TypeScript-%ED%8C%8C%EC%9D%BC%EB%AA%85-%EB%B3%80%EA%B2%BD-%EC%8B%9C-Already-included-file-name-%EC%98%A4%EB%A5%98)
