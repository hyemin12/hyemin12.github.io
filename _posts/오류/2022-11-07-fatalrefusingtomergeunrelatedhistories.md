---
title: git pull origin 명령어 오류
date: 2022-11-07 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

```bash
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to

hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

### 문제

프로젝트 구조를 변경하고, `git push origin master`를 통해 업로드 하려고 하였으나 `git pull`을 통해 프로젝트를 병합하라는 오류가 발생

오류 메세지를 따라 `git pull origin master`를 입력하였으나 해당 오류 발생

### 해결방법

```sh
git pull origin 브런치명 --allow-unrelated-histories
```

`--allow-unrelated-histories`  
이 명령 옵션은 이미 존재하는 두 프로젝트의 기록(history)을 저장하는 드문 상황에 사용된다고 한다. 즉, git에서는 서로 관련 기록이 없는 이질적인 두 프로젝트를 병합할 때 기본적으로 거부하는데, 이것을 허용해 주는 것이다.

[참고 블로그](https://gdtbgl93.tistory.com/63)
