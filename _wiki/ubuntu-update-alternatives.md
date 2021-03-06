---
layout  : wiki
title   : ubuntu update-alternatives 명령어 요약
summary : 
date    : 2020-08-22 17:14:12 +0900
updated : 2021-03-06 16:25:59 +0900
tag     : ubuntu update-alternatives ubuntu-command
toc     : true
public  : true
parent  : [[linux]]
latex   : false
---
* TOC
{:toc}

## 개요

`update-alternatives` 명령어는 하나의 명령어에 대해 두 개 이상의 버전이 존재할 때 디폴트로 실행할 버전을 설정하기 위해서 사용한다. 예를 들어, python2, python3 가 모두 설치돼있는 경우 `python` 커맨드를 실행할 때 기본으로 실행되는 버전은 python2이다. `python` 커맨드를 실행할 때 python3가 실행되도록 설정하기 위해 사용할 수 있다.

## user 별로 파이썬 버전 설정하기

간단히 `alias`를 설정하여 python3가 실행되도록 하는 방법도 있다. `~/.bashrc` 파일에 `alias`를 설정하고 터미널을 다시 켜면 적용 된 것을 확인할 수 있다.

```bash
alias python=`/usr/bin/python3`
```

## 시스템 전체의 기본 파이썬 버전 변경하기

시스템 전체에서 사용하는 파이썬 버전을 변경하고 싶다면 그때 `update-alternatives`를 사용할 수 있다.
auto mode에서는 우선순위가 높은 명령어가 실행된다. python3의 우선순위가 10, python2의 우선순위가 5이기 때문에 `python` 커맨드를 실행할 때 python3가 실행된다.

manual mode도 존재하는데, 기본값이 아닌 버전을 사용하고 싶을 때 명시적으로 선택하는 방법이다.

```bash
# python 커맨드에 등록된 binary 목록 표시
update-alternatives --list python

# python 커맨드에 python3 등록 (우선순위 10)
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 10

# python 커맨드에 python2 등록 (우선순위 5)
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 5

# manual mode로 버전을 선택
sudo update-alternatives --config python
```

## References

- <https://linuxhint.com/update_alternatives_ubuntu/>
- <https://linuxconfig.org/how-to-change-from-default-to-alternative-python-version-on-debian-linux>
- <https://askubuntu.com/questions/533391/what-does-priority-mean-in-update-alternatives>
- <https://gist.github.com/patrickmmartin/5b6b2ddecd29d6a1b2ffee2d8eea31ec>

