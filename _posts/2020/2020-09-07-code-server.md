---
layout  : post
title   : Visual Studio Code를 브라우저 상에 띄우기
summary : 태블릿으로 개발하기
date    : 2020-09-07T20:19:58+0900
tag     : vs-code visual-studio-code code-server
toc     : true
comment : true
public  : true
---
* TOC
{:toc}

## What is code-server?

코드 서버는 Visual Studio Code(vs code)를 웹 서비스 형태로 띄워서 브라우저에서 코딩할 수 있는 설치형 서비스다.

[cdr/code-server](https://github.com/cdr/code-server)

의아한 점은 이걸 왜 vs-code를 만든 마소에서 하지 않는가? visual studio code 자체는 오픈소스라서 활용이 가능하기 때문에 써드파티?에서 활용하여 이런 서비스를 만들 수 있다. 하지만 확장 기능 같은 경우는 Visual Studio 제품군에서만 사용할 수 있기 때문에 사용할 수 없고, 대신에 오픈소스로 동일한 기능을 하는 확장 기능을 만들어서 제공한다고 한다.

## How to setup?

### WSL Ubuntu 18.04 에 설치

처음 설치할 때는 도커를 띄워서 code-server를 컨테이너 형태로 설치하려고 했는데 내 옛날 노트북은 메모리가 4G밖에 안되는 구형 노트북이기 때문에 linux에 네이티브하게 설치하는 것으로 방향을 바꿨다. 설치 자체는 스크립트를 제공하기 때문에 간편하다.

```bash
curl -fsSL https://code-server.dev/install.sh | sh
```

설치가 완료되면 `code-server` 커맨드를 통해서 실행이 가능하다. 기본 설정으로 실행하면 `127.0.0.1:8080` 에 서버가 뜨고, `~/.config/code-server/config.yaml` 파일이 생성되는데, 이 파일 안에 있는 디폴트로 설정된 패스워드로 접속이 가능하다. 

그런데 http 로 접속시 일부 기능에 제약이 있어서 [https를 설정](/blog/2020/09/07/nginx-virtual-host/)하는게 보안적으로나 기능적으로 나은 것 같다. 내가 외부 접속을 열어두는 이유는 아이패드로 코딩을 해보려고 그런 건데, 태블릿에서 사용하는게 아니라면 굳이 이렇게 하지 않고 SSH forwarding을 통해서 접속하는 방법도 있다. [참조](https://github.com/cdr/code-server/blob/v3.5.0/doc/guide.md#ssh-forwarding)

### 실행

`code-server` 명령어를 통해서 실행한다. 그러면 `http://localhost:8080` 으로 vs-code 서버가 뜨고, 설정 파일들이 자동으로 생성된다. 서비스 형태로 실행도 가능하지만, `tmux` 세션을 만들어서 그 안에 띄워두었다.

`tmux new-session -t code-server` 이렇게  code-server용 세션을 만들고 내부에 code-server를 실행한다. 그러면 아래처럼 로그를 확인할 수 있다.

```bash
[2020-09-04T17:47:06.434Z] info  Wrote default config file to ~/.config/code-server/config.yaml
[2020-09-04T17:47:06.439Z] info  Using config file ~/.config/code-server/config.yaml
[2020-09-04T17:47:07.168Z] info  Using user-data-dir ~/.local/share/code-server
[2020-09-04T17:47:07.177Z] info  code-server 3.5.0 de41646fc402b968ca6d555fdf2da7de9554d28a
[2020-09-04T17:47:07.186Z] info  HTTP server listening on http://127.0.0.1:8080
[2020-09-04T17:47:07.186Z] info      - Using password from ~/.config/code-server/config.yaml
[2020-09-04T17:47:07.186Z] info      - To disable use `--auth none`
[2020-09-04T17:47:07.187Z] info    - Not serving HTTPS
```

### 패스워드 설정, 외부 접속 허용

앞에서 언급한 설정파일 `~/.config/code-server/config.yaml`에서 패스워드를 변경할 수 있다. `bind-addr: 0.0.0.0:8080` 으로 설정하면 외부에서도 접속이 가능하다. 그리고 패스워드 입력이 필요없는 경우에는 `code-server --auth none`으로 실행하거나 설정 파일에서 `auth: none` 으로 변경해주면 된다.

```bash
bind-addr: 127.0.0.1:8080
auth: password
password: 비밀번호
```

## 사용 소감

vs-code를 잠깐 사용해본 경험으로 비교해봤을 때 로컬에서 돌리는거랑 차이가 거의 없었다. 원래 vs-code가 electron기반이라 그런지 브라우저에서도 UI가 똑같았다. 그런데 내 노트북 서버에 문제가 좀 있는건지 연결이 자주 끊어지는점이 문제였다. 태블릿 가지고 다니면서 얼마나 코딩할지 모르겠지만, 해보고 더 자세한 후기를 업데이트 하도록 하겠다.

