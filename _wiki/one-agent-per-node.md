---
layout  : wiki
title   : 노드에 젠킨스 에이전트 하나씩만 띄울 수 있도록 설정하기
summary : Jenkins-kubernetes용 gradle agent 만들기
date    : 2022-07-20 22:45:03 +0900
updated : 2022-08-23 17:26:39 +0900
tag     : jenkins podAffinity podAntiAffinity
toc     : true
public  : true
parent  : [[Jenkins]]
latex   : false
---
* TOC
{:toc}

## 동기

젠킨스 에이전트(컨테이너)를 동적으로 만들고, 그 컨테이너들이 캐시를 공유하도록 하자는 아이디어는 좋았지만, gradle이 cache lock을 잡기 때문에 하나의 노드에서 동시에 gradle build를 할 수 없었다.

캐시를 사용하지 않는 것은 빌드 속도를 향상시키기 위해서 고려하지 않았다. 

처음엔 컨테이너에 캐시를 복사하는 방법을 사용했는데, 캐시가 비대해지면서 작업 시간이 늘어났고, 나중에는 캐시 복사가 안되는 지경에 갔다.

그래서 하나의 노드에서 병렬적으로 gradle build를 실행하는건 포기하고, 노드 볼륨 마운트로 캐시를 사용하되, 각 노드당 gradle build는 하나씩만 뜰 수 있도록 설정을 변경했다.

## 해결

`podAntiAffinity`를 활용해서 해당 레이블이 붙은 팟이 있는 경우, 그 노드에 스케쥴링 하지 않도록 설정하였다.

쿠버네티스를 쓰면 쓸수록 잘 만들었다고 느껴지는 부분이, 내가 생각한 시나리오대로 배포할 수 있는 스펙이 존재한다.

그러니 모르는게 있을 땐 공식 문서를 잘 살펴보자~!

```yaml
metadata:
  labels:
    build-dependency: gradle # gradle build용 agent에 label 설정

spec:
  usage: EXCLUSIVE
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
              - key: build-dependency
                operator: In
                values:
                  - gradle
```

## 참조

- [gradle lock 이슈](https://github.com/gradle/gradle/issues/851)
- [쿠버네티스 공식 문서](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/assign-pod-node/)
