---
layout  : wiki
title   : shell
summary : shell command cheat sheet
date    : 2020-06-12T11:19:33+0900
updated : 2020-09-11 18:20:27 +0900
tag     : shell bash zsh
toc     : true
public  : true
parent  : [[productivity]]
latex   : false
---
* TOC
{:toc}

# 운영 관련

## 프로세스 종료 (kill 하지않기)

`-s` 옵션을 통해 프로세스에 주는 신호(signal)을 선택하여 보낼 수 있다. `TERM` 신호는 프로세스가 해당 신호를 받아서 'graceful termination'을 가능하게 해준다. 반면에 `KILL` 신호는 프로세스를 강제로 종료시킨다. 

`kill -s TERM <pid>`

## 최근에 수정된 파일이나 폴더를 찾기

리눅스에는 세 가지 종류의 타임스탬프가 있다.

- atime: 액세스 시간 또는 마지막 액세스 시간
- mtime(modification): 수정 시간 또는 마지막 수정 시간
- ctime(change)      : 변경 시간 또는 마지막 변경 시간

modification과 change사이의 차이가 명확하지 않다.

- atime/amin: 커맨드 또는 프로그램에 의해 파일이 액세스된 마지막 시간
- mtime/mmin: 파일의 내용이 수정된 마지막 시간
- ctime/cmin: 파일의 속성이 수정된 마지막 시간

## ssh 서비스 부팅시에 자동으로 실행

보통 서버가 꺼질 일은 없겠지만 재부팅 시에 자동으로 ssh가 실행되도록 하는 경우 ssh 서비스가 실행 안되는 경우.. (홈서버 만들다 필요해서 찾아봄)

`sudo systemctl enable ssh`

# shell script 작성

쉘 스크립트 작성하면서 자주 찾아보는 것을 모아둔다. `man bash`를 참조해도 상세한 메뉴얼이 나온다.

## 스크립트 이름 가져오기

스크립트에 도움말이나, 사용법 등을 남기고 싶을 때 하드 코딩하지 않고 스크립트 이름을 가져오는 방법이다.

`prog=$(basename "$0")`

## 조건문 표현식

`man bash` 후 `CONDITIONAL EXPRESSION`을 검색하면 전체 리스트를 볼 수 있다.

- `-z string` 문자열 길이가 0일 때 True
- `-n string` 문자열 길이가 0이 아닌 경우 True
- `arg1 OP arg2`, `OP is -eq, -ne, -lt, -le, -gt, or -ge` 숫자에 대해서 참/거짓 연산. 순서대로 `==, !=, <, <=, >, >=` 과 같다.

활용 예를 간단히 들면 아래와 같다. `\[\[`는 `[`를 향상한 것이어서 조건문을 `[ -z "$RES" ]` 이렇게 써야할 것을 `[[ -z $RES \]\]` 이렇게 써도 되도록 해준다(변수를 따옴표로 감싸지 않아도 된다).

```bash
# 실행시 들어온 인자 카운트: 인자가 2가 아니면 메세지를 출력하고 프로그램을 종료한다.
if [ $# -ne 2 ]; then
    echo "should provide arg1 arg2"
	exit 1
fi

# 변수가 빈 문자열일 때
RES=$(some command)
if [[ -z $RES ]]; then
    echo "RES is empty"
fi
```

# 출처
- <https://superuser.com/questions/406920/linux-close-a-program-with-command-line-not-kill-it/406926>
- <https://www.2daygeek.com/check-find-recently-modified-files-folders-linux/>
- <https://stackoverflow.com/questions/3427872/whats-the-difference-between-and-in-bash>

