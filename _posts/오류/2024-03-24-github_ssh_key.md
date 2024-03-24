---
title: github ssh key 등록 오류
date: 2024-03-24 19:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

비공개 레포지토리를 사용하기 위해 ssh를 만들고 등록하였으나, 계속 `오류: 권한이 거부됨(publickey)` 오류 메세지가 출력되며 연결이 되지 않았다.

오류 내용을 검색해보니, 나같은 사람이 많았는지 [github 문서](https://docs.github.com/ko/authentication/troubleshooting-ssh/error-permission-denied-publickey)에 내용이 잘 정리되어있어 이를 보고 해결하였다.

먼저, `ssh -vT git@github.com`를 입력하여 도메인에 올바르게 연결하고 있는지 확인하였다.

```
ln: failed to create symbolic link '/etc/mtab': Permission denied
'C:\Windows\system32\drivers\etc\hosts' -> '/etc/hosts'
/usr/bin/cp: cannot create regular file '/etc/hosts': Permission denied
'C:\Windows\system32\drivers\etc\protocol' -> '/etc/protocols'
/usr/bin/cp: cannot create regular file '/etc/protocols': Permission denied
'C:\Windows\system32\drivers\etc\services' -> '/etc/services'
/usr/bin/cp: cannot create regular file '/etc/services': Permission denied
'C:\Windows\system32\drivers\etc\networks' -> '/etc/networks'
/usr/bin/cp: cannot create regular file '/etc/networks': Permission denied
rm: cannot remove '/etc/post-install/01-devices.post': Permission denied
rm: cannot remove '/etc/post-install/03-mtab.post': Permission denied
rm: cannot remove '/etc/post-install/06-windows-files.post': Permission denied
rm: cannot remove '/etc/post-install/99-post-install-cleanup.post': Permission denied
```

역시나 연결이 거부되었다고 출력이 되었다.

```
eval "$(ssh-agent -s)"
```

다음과 같은 명령어를 사용해 ssh-agent를 실행했고,

```
ssh-add -l -E sha256
```

명령어를 입력하여 프라이빗 키가 SHH에 들어있는지 확인하였다. 내 ssh-agent는 키를 가지고 있지 않다고 응답했다.

```
The agent has no identities.
```

**"ssh-agent에 내가 생성한 SSH 키가 등록되지 않아 연결이 되지 않는다."**라는 원인을 찾았고,

```
ssh-add ~/.ssh/id_ed25519
```

다음 명령어를 사용하여 ssh-agent에 내가 발급받은 ssh key를 등록하였다.

다시 ssh-agent에 등록된 key을 확인하여 잘 등록되어 있는 것을 확인하였다.

```
ssh-add -l -E sha256

256 SHA256:K
```

마지막으로 ssh key로 github 접속 가능한지 확인하였더니, 성공 메세지가 출력되었다.

```
ssh -T git@github.com

Hi hyemin12! You've successfully authenticated, but GitHub does not provide shell access.
```
