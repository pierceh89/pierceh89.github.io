---
layout  : post
title   : Certbot 이용하여 SSL 인증서 발급
summary : https 적용하기
date    : 2020-09-03T23:38:47+0900
tag     : certbot letsencrypt https
toc     : true
comment : true
public  : true
---
* TOC
{:toc}

iptime ddns를 통해서 접속이 가능하지만, http 프로토콜을 사용하기 때문에 로그인 시 패스워드가 노출될 위험이 있다. 그래서 Certbot을 통해 인증서를 받아서 https 프로토콜을 사용하도록 설정하려고 한다.

iptime ddns를 사용하면서 https를 사용할 수 있으면 좋았겠지만, 불가능하기 때문에 도메인이 필요하다. 내 경우는 구매한 도메인에 iptime ddns를 cname으로 설정해둔 상태다.

안되는 이유는 이 [블로그](https://hi098123.tistory.com/262)에서 확인했다. 

[Certbot](https://certbot.eff.org/)은 Let's encrypt 인증서 발급을 도와주는 도구라고 보면 된다. 

# 인증서 발급

1. 80포트로 서빙하는 서비스는 잠시 멈춘 뒤에, 
2. Certbot을  클론 받고, 
3. 스크립트를 실행하고 기다리면 인증서가 발급된다.

```bash
git clone https://github.com/certbot/certbot
sudo ./certbot-auto certonly --standalone -d 도메인
```

그러면 아래와 같이 메세지가 나오면서 인증서 발급이 성공했다고 알려준다.

```bash
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/도메인/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/도메인/privkey.pem
   Your cert will expire on 2020-11-26. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot-auto
   again. To non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

갱신 방법도 간단히 `certbot-auto renew` 명령어 한 줄로 할 수 있다.

# 적용

`/etc/letsencrypt/live/도메인/` 경로에 가면 `chain.pem`, `cert.pem`, `privkey.pem` 파일들이 있다. 이 파일들 권한이 root로 돼있기 때문에 실행시 권한 문제가 발생했다. 그래서 프로젝트 경로에 복사해준 뒤에 `chown $user:$user 파일이름` 커맨드를 통해 권한을  변경해줬다.

나는 플라스크 앱을 `gunicorn` 을 통해서 서빙하고 있는데 옵션에 인증서와 키를 설정하니 손쉽게 https 설정이 가능했다. 5000 포트를 포트포워딩으로 외부 포트 443으로 맞춰주면 끝! Certbot 덕분에 인증서 설정이 정말 쉽게 끝났다.

```bash
gunicorn --bind '0.0.0.0:5000' --ca-certs=chain.pem --certfile=cert.pem --keyfile=privkey.pem flaskr:app &
```

