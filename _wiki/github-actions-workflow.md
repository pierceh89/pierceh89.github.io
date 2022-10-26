---
layout  : wiki
title   : Github 워크플로우
summary : Github workflows를 이용해서 CI/CD를 자동화 해보자
date    : 2022-10-24 11:43:39 +0900
updated : 2022-10-26 10:39:45 +0900
tag     : github-actions github-actions-workflow
toc     : true
public  : true
parent  : [[Github-actions]]
latex   : false
---
* TOC
{:toc}

# Github workflow

![Workflow overview](https://docs.github.com/assets/cb-25535/images/help/images/overview-actions-simple.png)

1개 이상의 *Job* 을 실행하는 프로세스를 *workflow* 라고 부른다. YAML 파일 형식으로 되어 있고 이벤트를 통해 트리거하거나 직접 실행 또는 스케줄을 걸어서 실행할 수 있다. workflow는 `.github/workflows` 경로에 저장한다.

Job은 같은 러너에서 실행하는 여러 개의 *step* 으로 구성되어 있다. 각 step은 shell script 또는 *Action* 이다. step은 순서대로 실행되고 만약 실패하는 step이 있다면 그 다음은 실행되지 않는다. step 사이는 데이터를 공유할 수 있어서 어플리케이션을 빌드하는 스텝, 그 뒤에 빌드한 어플리케이션을 테스트하는 스텝을 구성할 수 있다.

Job은 기본 값으로 디펜던시가 없고 병렬적으로 실행되지만 다른 Job과 의존적으로 만드는 것도 가능하다. 만약 B Job이 A Job에 의존적이라면 B Job은 A Job이 완료되면 실행된다. 예를 들어 어플리케이션을 아키텍처 별로 빌드하는 Job들은 디펜던시 없이 병렬적으로 실행되지만 빌드를 패키징하는 Job은 빌드 Job에 의존적으로 실행 될 것이다.

Action은 반복적인 작업을 처리하는 Github Actions용 어플리케이션이다. Actions를 직접 작성할 수도 있고 마켓플레이스에서 이미 만들어진 Action을 사용하는 것도 가능하다.

## Jenkins declarative pipelines와 비교

Jenkins와 Github Actions 모두 자동화된 빌드, 테스트, 배포를 할 수 있는 워크플로우를 만들 수 있다. 그리고 워크플로우 설정 면에서 유사한 체계를 갖고 있다.

Jenkins는 *Declarative Pipelines* 라는 Github Actions workflow 파일과 비슷한 파일을 통해서 워크플로우를 생성한다. Jenkins는 step들을 실행하는 *Stage* 가 있고, Github Actions는 *Job* 이 그 역할을 한다. 그리고 둘 다 컨테이너 기반 빌드를 지원하고, 재사용 가능한 step을 지원한다.

차이점으로는 Jenkins는 groovy 언어 및 DSL을 사용해서 Declarative Pipeline을 작성하거나 Scripted Pipeline을 작성해야 하고 Github Actions는 YAML 포맷으로 workflow syntax를 이용한다. 또 Jenkins는 사용자가 직접 서버를 관리해줘야 하지만 Github은 자체적으로 제공해주는 클라우드 러너가 있고, self-hosted runner를 추가할 수 있는 하이브리드 구조를 지원한다.

[공식 문서](https://docs.github.com/en/actions/migrating-to-github-actions/migrating-from-jenkins-to-github-actions#comparing-capabilities)를 참고하면 각 명령어를 어떻게 대치할 수 있는지 표로 정리되어 있어서 마이그레이션이 쉽다. 그렇지만 groovy, java의 메소드를 사용한 workflow를 shell script로 대체하는 작업은 익숙하지 않아서 좀 힘들었다.

## Simple workflow

[상세한 설명](https://docs.github.com/en/actions/using-workflows/about-workflows#understanding-the-workflow-file)은 공식 문서를 참조하고 Github Actions를 사용해 본 경험에 대해서 말해보려고 한다. 우선 깃헙 -> 젠킨스로 왔다 갔다하는 일이 없어져서 편하다. 플러그인(Action)의 관리가 편리해졌다. 젠킨스 버전과 플러그인 버전 호환 문제로 몇 번 고생했더니 업데이트를 안하게 됐다. Actions는 runner의 버전에 따른 이슈가 생길지는 모르겠으나 action의 버전을 올리는 것은 부담없이 할 수 있다. 그리고 어지간한 Actions은 이미 만들어져 있어서 workflow를 빨리 만들 수 있는 것도 장점이었다.

```yaml
name: learn-github-actions
run-name: ${{ github.actor }} is learning GitHub Actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```

### Trigger

workflow를 트리거 하는 다양한 [이벤트](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)들이 있어서 CI/CD 자동화 뿐만 아니라 프로젝트 관리를 자동화 할 수 있는 장점이 있다. 이슈가 생성 됐을 때 라벨링을 해준다던지, 릴리즈가 생성됐을 때 패키징을 하는 식으로 다양한 방식으로 활용이 가능하다.

### Secret, Environment 활용

Workflow를 실행할 때 민감한 정보를 전달해야 하는 경우는 [Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)으로 등록해서 사용할 수 있다. Organization 레벨 시크릿을 등록하면 여러 리파지토리에서 공유하는 것도 가능하다. secret은 등록 시 [libsodium sealed box](https://libsodium.gitbook.io/doc/public-key_cryptography/sealed_boxes)로 암호화되어 전송되고 저장된다. 한번 등록된 시크릿은 웹에서 볼 수 없다. 혹시나 싶어서 secret을 일부러 {% raw %}`echo ${{ secret.SOME_TOKEN }}`{% endraw %}명령을 넣어서 로그에 찍히는지 확인해봤는데 마스킹 처리를 해줘서 노출되지 않았다. 추가로 [Environment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)를 활용하면 `production`, `stage` 처럼 배포 타겟에 따라 secret 값을 다르게 설정하는 것도 가능하다.

## Reusable workflow & Composite action

위에서 설명했듯이 action은 반복적인 작업을 대체해주는 step이다. 어플리케이션 빌드를 하는 워크플로우를 만든다고 했을 때, 체크아웃, 빌드 환경 설정, 빌드, 도커 이미지 생성 및 푸시 같은 단계들은 step 이고 커스텀 action을 통해 간결하게 관리할 수 있다. 이 파이프라인 전체를 재사용하고 싶다면 어떻게 해야 할까? 바로 [Reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows)를 작성하면 된다.

그런데 워크플로우를 재사용하기 전에 확인 해야 할 것이 액세스 정책이다. public 리파지토리에 저장된 워크플로우만 재사용 할 수 있다. organization의 리파지토리는 외부 노출을 막기 위해 internal 타입만 사용해야 할 수 있다. 이런 경우에는 다른 방법을 찾아야 한다.

[Composite Actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)은 다수의 워크플로우 스텝을 결합하여 하나의 액션으로 실행할 수 있도록 해준다. custom action을 javascript 없이 shell script만으로 만들 수 있고, 빌드 및 배포 workflow를 하나의 스텝으로 실행할 수 있어서 대안으로 활용하였다. composite action을 활용한 결과 다음과 같이 워크플로우를 간결하게 작성할 수 있었다.

{% raw %}
```kotlin
name: DEV CI/CD

on:
  push:
    branches: [ 'feature/*' ]

jobs:
  build-and-deploy:
    runs-on: [ ubuntu-latest ]
    steps:
      - name: checkout actions
        uses: actions/checkout@v3
        with:
          repository: pierceh89/custom-actions
          ref: v2
          token: $GITHUB_TOKEN
          path: actions
      - name: call-ci
        id: call-ci
        uses: ./actions/ci
        with:
          DOCKER_REPO: hub.docker.com/some-project
      - name: call-deploy
        if: ${{ success() }}
        uses: ./actions/cd
        with:
          PROJECT_NAME: some-project
```
{% endraw %}

`checkout actions`에서 action을 관리하는 `custom-actions` 리파지토리를 체크아웃 한다. 다음 스텝인 `call-ci`에서 `ci` action을 사용하여 빌드를 수행한다. 그리고 성공하면 `call-deploy`에서 배포를 수행한다. 젠킨스를 사용할 때 처럼 빌드 & 배포 스크립트를 한 곳에서 관리하면서 리파지토리 별로 상세한 운영 방식은 따로 가져갈 수 있어서 좋아 보인다. 아쉬운 점이 있다면 action을 shell-script로만 작성해서 [툴킷](https://github.com/actions/toolkit) 등 도구를 활용하지 못하고 있는데 다음 버전은 javascript로 작성을 해봐도 좋을 것 같다.
