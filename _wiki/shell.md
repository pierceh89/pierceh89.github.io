---
layout  : wiki
title   : shell
summary : shell command cheat sheet
date    : 2020-06-12T11:19:33+0900
updated : 2020-08-29 14:39:02 +0900
tag     : shell bash zsh
toc     : true
public  : true
parent  : [[productivity]]
latex   : false
---
* TOC
{:toc}

# 프로세스 종료 (kill 하지않기)

`-s` 옵션을 통해 프로세스에 주는 신호(signal)을 선택하여 보낼 수 있다. `TERM` 신호는 프로세스가 해당 신호를 받아서 'graceful termination'을 가능하게 해준다. 반면에 `KILL` 신호는 프로세스를 강제로 종료시킨다. 

```bash
kill -s TERM <pid>
```

# 최근에 수정된 파일이나 폴더를 찾기

리눅스에는 세 가지 종류의 타임스탬프가 있다.

- atime: 액세스 시간 또는 마지막 액세스 시간
- mtime(modification): 수정 시간 또는 마지막 수정 시간
- ctime(change)      : 변경 시간 또는 마지막 변경 시간

modification과 change사이의 차이가 명확하지 않다.

- atime/amin: 커맨드 또는 프로그램에 의해 파일이 액세스된 마지막 시간
- mtime/mmin: 파일의 내용이 수정된 마지막 시간
- ctime/cmin: 파일의 속성이 수정된 마지막 시간

# ssh 서비스 부팅시에 자동으로 실행

`sudo systemctl enable ssh`

# 출처
- <https://superuser.com/questions/406920/linux-close-a-program-with-command-line-not-kill-it/406926>
- <https://www.2daygeek.com/check-find-recently-modified-files-folders-linux/>

