---
layout  : wiki
title   : 젠킨스 에이전트의 빌드 캐시 구성
summary : cache lock을 우회해보자
date    : 2022-07-20 22:43:37 +0900
updated : 2022-08-23 21:06:00 +0900
tag     : jenkins jenkins-agent gradle
toc     : true
public  : true
parent  : [[Jenkins]]
latex   : false
---
* TOC
{:toc}

## 동기

지금은 사용하지 않지만, 하나의 노드에서 gradle build를 병렬적으로 실행하려고 사용했던 방법이다.

컨테이너를 통해 빌드를 할 때 호스트의 gradle 경로를 공유하면 `gradle.lock` 때문에 충돌이 생긴다. 문서에 따르면 두 가지 방법을 제시하고 있다.

- [컨테이너에 디펜던시 캐시 복사](https://docs.gradle.org/current/userguide/dependency_resolution.html#sub:cache_copy)
- [다수이 컨테이너 사이에 읽기전용 디펜던시 캐시 공유](https://docs.gradle.org/current/userguide/dependency_resolution.html#sub:shared-readonly-cache)

## 컨테이너에 디펜던시 캐시 복사

당시에는 gradle 버전이 낮아서 컨테이너에 디펜던시 캐시를 복사하는 방법을 선택했었다. 그리고 읽기 전용 캐시는 인큐베이팅 피처기도 해서 사용하지 않았다.

### gradle cache 복사

> The path that can be copied is $GRADLE_HOME/caches/modules-\<version\>. 
The only constraint is placing it using the same structure at the destination, where the value of GRADLE_HOME can be different.
Do not copy the *.lock or gc.properties files if they exist.

아래의 명령어를 통해 `*.lock`, `gc.properties`를 제외하고 `caches`, `wrapper` 디렉토리만 호스트 gradle 경로에서 복사해서 컨테이너를 초기화한다.

`(cd /home/deploy/.gradle && tar cf - --exclude=*.lock --exclude=gc.properties caches wrapper) | (cd /root/.gradle && tar xf - );`

### 호스트 gradle에 추가된 디펜던시 반영

새로운 프로젝트를 생성하거나 디펜던시를 추가한 경우 컨테이너에서 새로 다운로드된 캐시를 호스트에 반영해줘야 한다. 추가된 디펜던시만 찾아서 업데이트 할 수 있도록 개선하면 좋을 것 같다.

`cd /root/.gradle && tar cf - --exclude=*.lock --exclude=gc.properties caches wrapper) | (cd /home/deploy/.gradle && tar xf - --skip-old-files);`

## 참조

- [gradle lock 이슈](https://github.com/gradle/gradle/issues/851)
- [Dealing with ephemeral builds](https://docs.gradle.org/current/userguide/dependency_resolution.html)
