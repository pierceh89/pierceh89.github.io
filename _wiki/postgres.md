---
layout  : wiki
title   : 
summary : 
date    : 2020-08-29 14:41:15 +0900
updated : 2021-03-06 16:28:53 +0900
tag     : postgresql
toc     : true
public  : true
parent  : [[programming]]
latex   : false
---
* TOC
{:toc}

postgresql을 개인프로젝트 하는김에 배운다 생각하고 써본건데, MySQL이랑 너무 달라서 힘들었고 수박 겉핥기 식으로 구글링해서 문제가 생길때마다 해결한 적이 많았다. 그래서 또 구글링 안해도 될 정도로 정리만 해보려고 한다.

## Install
 
[링크](https://www.postgresql.org/download/linux/ubuntu/) 의 스크립트를 참조하여 설치한다.

## TroubleShooting

### sequence being not updated 

디비를 백업하고 테이블을 초기화한 뒤에 데이터를 옮겨줬는데 그 후에 insert가 안되는 현상이 발생했다.  
데이터 옮기는 작업을 GCP 인스턴스에서도 해보고 내 노트북에도 해봤는데 처음에는 문제가 없었기에 더 혼란이었다.  
에러메세지는 아래 스택오버플로우 질문과 마찬가지로 `Key (id)=(1) already exists` pk가 이미 존재한다는 식으로 나왔다.  
postgres에서 auto increment 되는 키의 값을 저장하는 변수? 가 있는데 그 값이 업데이트 되지 않아서 발생한 현상이었다.  
`users` 테이블에 `id`라는 primary key가 있을 때 `id`의 시퀀스를 저장하는 변수의 이름이 `users_id_seq`라는 값으로 자동 생성이 되는데, 이 시퀀스의 값을 `max(id)`로 업데이트하면 된다.  
그래서 `SELECT setval('users_id_seq', max(id)) FROM users;` 이런식으로 시퀀스 값을 업데이트해주면 더이상 에러가 발생하지 않는다.   

<https://stackoverflow.com/questions/44708548/postgres-complains-id-already-exists-after-insert-of-initial-data/44708862>

## postgres commands

postgres 명령어들은 MySQL 의 그것들과 너무 달라서 처음에 대혼돈을 겪었다...  
cli로 작업을 해보려고 했지만 너무 속도가 안나서 pgAdmin과 IntelliJ datasource 같은 gui 툴을 많이 사용했지만,  이 툴에 연결시키려면 설정을 바꿔서 포트를 열어주는 작업을 또 해줘야하기 때문에 급한대로 사용했던 커맨드들을 모아봤다.  

```sh
# user를 postgres로 하고 psql 실행 (postgres client 접속)
sudo -u postgres psql

# postgres 서비스 실행
sudo pg_ctlcluster 12 main [start|stop|restart]

# user의 password 변경
\password

# DB_NAME 으로 디비 생성
CREATE DATABASE DB_NAME

# DB_NAME 으로 현재 사용하는 디비 변경 (use DB_NAME과 동일)
\c DB_NAME

# 현재 선택한 DB 내에 있는 테이블 목록 출력
\dt

# DB_NAME 디비에 대해 sqlfile 실행
sudo -u postgresql psql -d DB_NAME -f sqlfilename
```

## postgres port 열어주기

ubuntu에 postgres를 설치한 경우를 기준으로 `/etc/postgresql/12/main` 디렉토리에 있는 `pg_hba.conf`, `postgresql.conf` 두 파일을 수정해줘야한다.

### pg_hba.conf

IPv4 local connections 부분에서 아이피 부분을 아래처럼 `0.0.0.0/0`으로 변경한다.

```
## IPv4 local connections:
host    all             all             0.0.0.0/0            md5
```

### postgresql.conf

`listen_addresses` 디폴트로 주석처리가 되있는데 주석처리를 풀고 값을 아래처럼 `'*'`로 변경한다.

```
## - Connection Settings -

listen_addresses = '*'          # what IP address(es) to listen on;
```


