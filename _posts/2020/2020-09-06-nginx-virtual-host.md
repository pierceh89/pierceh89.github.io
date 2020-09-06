---
layout  : post
title   : Nginx virtual host 설정, Certbot 이용하여 Let's encrypt 인증서 발급
summary : 전부 https를 적용하자
date    : 2020-09-07T00:11:46+0900
tag     : nginx virtual-host certbot lets-encrypt
toc     : true
comment : true
public  : true
---
* TOC
{:toc}

내가 가비아에서 구매한 도메인은 혜자스럽게도 서브도메인 등록도 가능했다. 그래서 [code-server](https://github.com/cdr/code-server)를 설치해서 아이패드로 코딩할 수 있게 환경 설정을 해봤다. cname에 마찬가지로 ddns를 넣고 포트를 붙여서 설정하려고 했는데 지원하지 않는 기능이라고 한다. 

그러면 ddns로는 웹서비스를 하나만 제공할 수 있는 걸까? 다행히도, nginx virtual host를 사용하면 요청이 들어오는 도메인에 따라서 응답하는 서비스를 다르게 설정이 가능하다고 한다. 

flask 앱 하나만 서비스 할 때는 `gunicorn`을 사용했었는데, nginx를 통해서 서비스 할 때는 `uwsgi` 가 필요하다고 한다. [공식 문서](https://flask.palletsprojects.com/en/1.1.x/deploying/uwsgi/) 을 보고 따라서 설정해봤다.

```bash
export PYTHON_PATH=/application_path
uwsgi -s /tmp/app.sock --manage-script-name --mount /=flaskr:app --chmod-socket=666
```

`PYTHON_PATH` 는 플라스크 앱의 경로를 지정해주고,  `-s /tmp/app.sock` 는 nginx와 연결할 소켓의 이름을 설정하는 것이고, `--manage-script-name` 은 uwsgi 에 `SCRIPT_NAME` 의 관리를 uwsgi에 맡긴다는 것인데 정확히 어떤 의미인지는 모르겠다. `--mount /=flaskr:app` 은 / (루트, http://domain.name/) 의 요청을 flaskr 패키지의 app 이라고 명명한 flask 앱에게 전달한다는 뜻이다. `--chmod-socket=666` 설정은 nginx에서 소켓을 사용할 권한을 주기 위해 추가하였다. 

다음은 nginx 설정을 추가해야 한다.

우분투에서 nginx를 설치하면 `/etc/nginx/sites-available` , `/etc/nginx/sites-enabled` 두 디렉토리가 있다. 앞의 디렉토리에 설정파일을 먼저 만든 뒤에 심볼릭 링크를 만들어서 뒤의 폴더에 두는 식으로 설정을 하여 관리를 편하게 할 수 있다.

```bash
server {
    server_name domain.name www.domain.name;

    location / { try_files $uri @app; }
    location @app {
        include uwsgi_params;
        uwsgi_pass unix:///tmp/app.sock;
    }
}
```

위의 스크립트는 기존에 서비스하던 플라스크 앱이 설정이다. 마찬가지로 공식 도큐멘테이션을 참고하였다. `server_name` 에 설정한 도메인 이름을 넣고,  `@app` 의 app은 `flaskr:app` 의 app에 대응하고,  `uwsgi_pass` 에는 위에서 설정한 소켓의 경로를 넣어주면 된다.

설정한 도메인으로 접속이 잘 되는것을 확인했으면 certbot을 이용해서 인증서를 받으면 된다. nginx 설정은 certbot이 알아서 변경해줘서 https 적용이 한번에 가능하다!

```bash
sudo certbot --non-interactive --redirect --agree-tos --nginx -d domain.name -d www.domain.name
```

code server도 마찬가지로 [공식 문서](https://github.com/cdr/code-server/blob/v3.5.0/doc/guide.md#nginx)를 참고해서 설정했다.  그런데 https설정을 한 뒤에 빈 화면이 뜨는 현상이 있어서 좀 삽질을 하다 설정을 추가하여 해결할 수 있었다. [참조 링크](https://github.com/cdr/code-server/issues/1362)

```bash
server {
    server_name sub.domain.name;

    location / {
      proxy_pass http://localhost:8080/;
      proxy_set_header Host $host;
      proxy_set_header Upgrade $http_upgrade;
      #proxy_set_header Connection upgrade;
      proxy_set_header Accept-Encoding gzip;

      # 공식문서에서 언급되지 않은 설정들
      proxy_set_header Connection "Upgrade";
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For  $proxy_add_x_forwarded_for;
      proxy_http_version 1.1;
    }
}
```

설정을 추가한 뒤에는 마찬가지로 certbot을 사용해서 sub.domain.name 에 대해 인증서를 받으면 https 설정이 완료된다.

