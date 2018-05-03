---
layout: post
title: AWS에서 Redis 설치 및 데몬으로 설정하기
comments: true
excerpt: Install Redis on AWS and run as daemon
tags:
- aws
- devops
---
레디스를 aws에 설치하고, 단순히 redis-server를 실행해서 서버를 구동시킬 수 있지만, 좀 더 정교하게 설치할 수 있는 방법에 대해서 알아본다.

{% highlight bash %}
# AWS에는 gcc가 기본으로 설치되어있지 않기 때문에 설치해줘야 한다.
$ sudo yum install gcc
$ wget http://download.redis.io/redis-stable.tar.gz
$ mv redis-stable /usr/local/
$ sudo tar xvzf redis-stable.tar.gz
$ cd redis-stable
$ sudo make
$ sudo make install
# zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: 에러가 날 경우
make distclean 한 뒤에 make 해준다.
{% endhighlight %}

이제 레디스는 설치되었고, 레디스를 구동할 스크립트를 설정해줘야 한다. 이전에 레디스 환경설정 및 덤프를 저장할 폴더를 생성해준다.
{% highlight bash %}
$ sudo mkdir /etc/redis
$ sudo mkdir /var/redis
$ sudo mkdir /var/redis/6379 #레디스를 포트별로 구분하여 관리하기 위해 폴더를 하나 더 생성한다.
{% endhighlight %}

스크립트를 복사하고, **REDISPORT**를 설정에 맞게 수정해준다. (여기서는 기본인 6379로 설정)
{% highlight bash %}
$ sudo cp utils/redis_init_script /etc/init.d/redis_6379
{% endhighlight %}

그리고 설정 파일을 복사한 뒤, 다음 설정들을 찾아 설정한다. 역시 포트 번호에 맞게 파일명을 설정한다.
{% highlight bash %}
$ sudo cp redis.conf /etc/redis/6379.conf
# chkconfig: 2345 64 36 이 라인은 Amazon Linux (Redhat계열)에서 사용하기 위한 설정을 정의한 라인이다.
daemonize -> yes
pidfile -> /var/run/redis_6379.pid
port -> 6379
loglevel -> 넷 중에 하나를 고른다, notice가 기본값
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
logfile -> "/var/log/redis_6379.log"
dir -> /var/redis/6379
{% endhighlight %}

서비스를 등록한다.
{% highlight bash %}
$ sudo chkconfig --add /etc/init.d/redis_6379
{% endhighlight %}

모든 설정이 완료됐다. 이제부터는 레디스 인스턴스를 실행할 때 다음과 같이 명령을 입력하면 된다.
{% highlight bash %}
$ sudo /etc/init.d/redis_6379 start
$ redis-cli ping   # test
PONG
{% endhighlight %}

참고문서:

[Reids Quickstart](https://redis.io/topics/quickstart)