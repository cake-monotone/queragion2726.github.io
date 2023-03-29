---
title: 특정 서버를 경유해서 ssh 사용하기
tags: [ ssh ]
category: programming
date: 2021-08-08 00:00:00 +0900
lng_pair: id_ssh_J
---

# 특정 서버를 경유해서 ssh 사용하기

살다보면 "외부 네트워크에서 곧바로 접근할 수 없는 서버"에 ssh로 접근하고 싶은
일이 생깁니다.

더 정확히는 "내부 네트워크와 연결되어 있으나, 외부에서 접근하기 위해서는 내부
네트워크의 다른 서버를 경유해서 이용해야하는 경우"를 말합니다. 문장이 너무
복잡하니 그림으로 봅시다.

![서버구조_베이스](:ssh-diagram-base.png){:data-align="center"}

### 예시

어떤 경우에 이렇게 복잡하게 접근하게 될까요. 다음과 같은 경우들이 있습니다.

- 회사 내부 로컬 네트워크에 연결된 VM에 접근하고 싶은 경우

![서버구조1](:ssh-diagram1.png){:data-align="center"}

- 서버 내부에 설치된 도커 컨테이너에 ssh로 접근하고 싶은 경우

![서버구조2](:ssh-diagram2.png){:data-align="center"}

이 밖에도, 방화벽 등의 이유로 특정 서버를 경유해서 들어가야할 경우가 생깁니다.

# 단순한 해결법

해결 방법은 간단해 보입니다.

먼저 jump server에 ssh 로 로그인한 뒤, remote server에 다시 ssh로 접근하면
됩니다.

```sh
$ ssh jump_server
$ (jump_server) ssh remote_server
```

한두번 접속한다면 이보다 좋은 방법은 없습니다. 하지만 다음과 같은 단점이
있습니다.

- ssh key 인증을 사용하기 까다롭습니다.

jump server까지는 사용할 수 있지만, remote server에 접속하기 위해서는 jump
server에 private 키를 두고 있어야 합니다. 이는 위험할 뿐더러 관리하기 힘듭니다.

- ssh config를 사용하기 까다롭습니다.

ssh key 인증과 마찬가지로, jump server에 따로 config를 설정해야 합니다. 누가
바꿔놓을 가능성이 있어 위험하고, 관리도 번거롭습니다.

- scp를 사용하기 까다롭습니다.

파일을 jump server에 노출해야합니다. 마찬가지로 파일이 노출되는 문제가 있습니다.

# ssh -J

J 옵션은 이런 경우를 위해 마련된 옵션입니다. (ssh version >= 7.3)

```sh
ssh -J user1@jumpserver:port user2@remoteserver:port
```

위 명령어로 jump server를 경유해 remote server에 바로 접근할 수 있습니다. 정말
별다른 수고로움 없이, ssh서버만 작동하고 있다면 이 간편한 기능을 사용할 수
있는거죠. (단, ssh 서버측 설정으로 제한 가능)

# ssh config로 간편하게 설정하기

config에는 `ProxyJump`를 사용해 설정할 수 있습니다.

```
# ~/.ssh/config

Host jump-server
    HostName jump_hostname
    User user1

Host remote-server
    HostName remote_hostname
    User user2
    ProxyJump jump-server
```

<p></p>

```sh
ssh remote-server
```

IdentityFile 도 훌륭하게 작동합니다. 즉, ssh에 key를 쓰고 싶으시다면, config에
IdentityFile 옵션을 추가하시면 됩니다.

```
# ~/.ssh/config

Host jump-server
    HostName jump_hostname
    User user1
    IdentityFile ~/.ssh/id_jump_server_key

Host remote-server
    HostName remote_hostname
    User user2
    IdentityFile ~/.ssh/id_remote_server_key
    ProxyJump jump-server
```

이렇게 세팅을 완료하면, scp도 정말 간편하게 사용할 수 있습니다.

```sh
scp ~/my_file remote-server:/home/user2/my_file
```

위 명령은, jump server를 경유해서 remote-server에 파일을 보냅니다.

# 그 외

사실 -J 옵션과 `ProxyJump`는 선후관계가 역전되었는데요. `ProxyJump`이 원형이고,
-J 옵션이 그 `ProxyJump` shortcut입니다.
[man 페이지 참조](https://man.openbsd.org/ssh#J){:target="_blank"} 따라서 -J
옵션은 조금 사용성이 제한된 느낌이 있습니다. (-i 옵션을 J안에 줄 수 있는 방법이
없습니다)

추가로 이 밖에도, 생각보다 ssh가 지원하는 기능이 많습니다.

특히 -L (Port Forwarding) -R (Remote Port Forwarding) 옵션은 `ProxyJump`와 같이
사용하기 매우 좋으니, 알아보시는걸 추천드립니다.
