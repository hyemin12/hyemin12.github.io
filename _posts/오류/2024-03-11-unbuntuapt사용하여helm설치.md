---
title: unbuntu apt을 사용하여 helm을 설치할 때 발생한 오류
date: 2024-03-11 14:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

3월 11일 부트캠프 강의를 수강하며, unbuntu apt을 사용하여 helm 패키지를 설치하는 과정에서 오류가 발생하였다.

Helm을 받아오기 위한 apt repo 설정을 하면 다음과 같은 메시지가 출력되어야하는데, 줄바꿈되어 출력되었다.

- 성공 메세지

```
deb [arch=amd64 signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main
```

- 출력된 메세지

```
deb [arch=amd64 \
signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main
```

줄바꿈이 큰 문제가 있겠어? 하고 helm 패키지를 설치하는 명령어를 입력하였으나 다음과 같은 오류가 발생하였다.

```
$ sudo apt install -y helm

E: Malformed entry 1 in list file /etc/apt/sources.list.d/helm-stable-debian.list ([option] too short)
E: The list of sources could not be read.
```

`/etc/apt/sources.list.d/helm-stable-debian.list` 파일을 찾을 수 없다는 오류이다. 이를 해결하기 위해 해당 파일을 수정하였다.

- 수정 명령어

```
sudo nano /etc/apt/sources.list.d/helm-stable-debian.list
```

명령어를 입력하며, 해당 파일을 편집한 후에 `ctrl + x`를 누르고 `Y`를 눌러 변경사항을 저장하고 `enter`키를 눌러 편집기를 빠져나오면 된다.

위의 작업을 통해 변경사항을 저장하고 다시 helm을 설치하기 위해 명령어를 입력하였으나 다음과 같은 오류 메세지가 출력되었다.

```
dpkg was interrupted, you must manually run 'sudo dpkg --configure -a' to correct the problem
```

시스템 패키지 관리자가 패키지 설치 중에 중단이 되었다는 오류 메세지이다. 중단된 작업을 재구성하고 완료하는 명령어를 입력하면 해결된다.

```
sudo dpkg --configure -a
```

벌써 세번째 오류...  
패키지를 가져오는 과정에서는 오류가 발생하지 않았지만, 다음과 같은 오류 메세지가 출력되며 패키지 설치가 정상적으로 이루어지지 않았다.

```
$ sudo apt install -y helm
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done

No apt package "helm", but there is a snap with that name.
Try "snap install helm"

E: Unable to locate package helm
```

시스템 패키지 관리자가 helm 패키지를 찾지 못한다는 오류 메세지이며, snap을 사용하면 helm을 설치할 수 있다고 해결 방안을 제시해줬다.

하지만 apt를 사용해서 해당 패키지를 설치하고 싶었고, [공식문서](https://helm.sh/ko/docs/intro/install/)에서 해결 방안을 찾았다. 공식문서에 ubuntu apt로 설치하는 명령어가 있어서 이를 입력하였더니, 성공적으로 helm 패키지가 설치되었다.

명령어 여러 개를 한번에 입력하고 실행하기때문에 명령어 끝에 세미콜론(`;`)을 붙여서 명령어를 구분해주었다.

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null; \
sudo apt-get install apt-transport-https --yes; \
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list; \
sudo apt-get update; \
sudo apt-get install helm
```

또는

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null; sudo apt-get install apt-transport-https --yes; echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list; sudo apt-get update; sudo apt-get install helm
```

성공적으로 패키지 설치가 완료되었는지 확인하고 싶다면 버전을 확인하는 명령어를 실행하면 된다.

```
$ helm version
```

- 출력메세지

```
version.BuildInfo{Version:"v3.14.2", GitCommit:"c309b6f0ff63856811846ce18f3bdc93d2b4d54b", GitTreeState:"clean", GoVersion:"go1.21.7"}
```
