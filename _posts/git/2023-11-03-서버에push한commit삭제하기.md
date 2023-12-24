---
title: 서버에 push한 commit 삭제하기
date: 2023-11-03 13:24:00 +09:00
categories: ["git"]
tags: ["git"]
toc: true
toc_sticky: true
---

## #서버에 push한 commit 삭제하기

### - git reset (레포지토리를 혼자 사용할 때)

#### 1. git commit 내역을 확인하고, 삭제할 commit 확인하기

`git log`

#### 2. `reset` 명령어를 이용하여 commit 삭제하기

■ 가장 최근 명령어를 지우고 싶다면

`git reset HEAD^`

■ 여러개의 commit 이전으로 돌리는 경우

`git reset HEAD~2`

※ 돌아간 커밋 이후의 변경 이력을 모두 삭제하고 싶다면 `--hard` 옵션 사용하기

`git reset --hard [commit hash]`

#### 3. commit을 지웠다는 것을 서버에 알려주기

`git push -f origin [branch]`

※ 해당 레포지토리를 다른 사람과 공유하고 있다면 절대로 사용하면 안됨!

### - git revert

revert 명령어는 reset과는 다르게 커밋을 삭제하는 것을 추가하는 방식으로 코드를 되돌림

#### 1. 돌아가고자 하는 commit ID를 적기

`git revert [commit hash]`

#### 1-1. `--no-commit` 옵션을 사용해서 바로 커밋되지 않게 하기

`git revert  --no-commit  [commit hash]`

#### 1-2. 변경 내용 commit message 작성

`git commit -m [message]'

#### 1-3. 변경 내용 서버에 올리기

`git push origin [branch]`

---

### reset

장점 :

- 커밋 히스토리를 깔끔하게 유지할 수 있음
- 혼자 작업할 때 편하게 되돌아갈 수 있음

단점:

- 다른 사람과 같은 브랜치에서 작업할 때 커밋이 뒤섞일 수 있음

### revert

장점:

- 중간에 무슨 문제가 있었는지, 왜 돌아갔는지 등의 기록이 가능함
- 다른 사람과 같은 브랜치에서 함께 작업할 때 코드 충돌을 최소화할 수 있음

단점:

- 커밋 히스토리에 변경 내역이 커밋에 남음

참고사이트:

- [git reset, revert로 이전 커밋으로 돌리기](https://kyounghwan01.github.io/etc/git/git-reset-revert/#revert)
