---
layout  : wiki
title   : kubernetes 클러스터에 젠킨스 설치해서 사용하기
summary : 컨테이너를 사용해서 CI/CD 파이프라인 구축하기
date    : 2022-07-20 22:42:36 +0900
updated : 2022-08-23 22:05:27 +0900
tag     : jenkins kubernetes devops
toc     : true
public  : true
parent  : [[Jenkins]]
latex   : false
---
* TOC
{:toc}

## helm chart를 통해서 설치

```bash
helm repo add jenkins https://charts.jenkins.io
helm repo list
helm repo update
```

helm chart를이용해서 설치하는 것이 쉽고, 관리하기도 편리하다. 먼저 repository를 등록한 뒤에 `helm show values jenkins/jenkins > values.yml` 명령어를 통해서 default value를 확인한다. 각 설정값마다 주석이 있어서 이해하기 어렵지 않지만 필요하면 [레퍼런스](https://github.com/jenkinsci/helm-charts/blob/main/charts/jenkins/VALUES_SUMMARY.md) 문서를 통해 확인해도 된다.

### dedicated jenkins image

기본값에는 image가 `jenkins/jenkins`로 설정되어 있어서 버전이 추가되면 추후 컨테이너 재시작 때 원치 않은 업그레이드가 일어날 수 있다. 플러그인이 그대로 작동하면 다행인데, 종종 플러그인이 작동하지 않을 때가 있다. 그래서 이것 저것 업데이트 하다 보면 젠킨스 구동이 안 될 때가 있다. (디펜던시 지옥 시작..)

그래서 젠킨스 이미지는 특정 버전을 명시해주는것이 좋다. 나아가 기본 이미지에 원하는 플러그인을 다운로드 받아서 미리 세팅을 한 뒤에 그 이미지를 이용해서 젠킨스를 구동하면 플러그인때문에 고생하는 일은 좀 덜 수 있다. 요새는 Jenkins Configuration as Code (JCasC)가 나와서 젠킨스 설정을 코드로 관리할 수 있는 플러그인도 있다.

### jenkins volume

Persistent Volume(PV)를 통해서 젠킨스 마스터의 설정을 유지를 위해 사용한다. PV가 없으면 마스터 컨테이너가 어떤 이유로 재시작하는 경우 설정을 다시 해야한다. 추가로, 젠킨스 에이전트 컨테이너들이 캐시를 공유해서 사용하는 데에도 활용할 수 있다. PV 설정이 어렵다면 host path를 사용해서 설정하는 방법도 있다.

### pod template

젠킨스 에이전트는 팟 스펙으로 구현할 수 있다. 원하는 빌드에 따라 컨테이너를 선언해서 사용하면 편리하다. 예를 들어 자바8 빌드, 자바11 빌드, golang 빌드용 스펙을 각각 정의해서 git에 저장하고 가져다 쓰면 편리하게 사용할 수 있다.

```yaml
metadata:
  labels:
    name: jenkins-agent

spec:
  usage: EXCLUSIVE
  containers:
    - name: docker
      image: docker
      tty: true
      workingDir: /home/jenkins/agent
      command: ["/bin/sh", "-c"]
      args: ["cat"]
      volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
    - name: go
	  ...
	- name: kubectl
	  ...
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
```

## 참조

- [Installing Jenkins on kubernetes](https://www.jenkins.io/doc/book/installing/kubernetes/)
- [JCasC](https://github.com/jenkinsci/configuration-as-code-plugin)
- [Local Storage](https://kubernetes.io/docs/concepts/storage/storage-classes/#local)
