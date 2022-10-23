---
layout  : wiki
title   : Github packages
summary : 이제 package도 github 안에서 관리하자
date    : 2022-07-23 00:05:59 +0900
updated : 2022-10-23 18:32:01 +0900
tag     : github github-packages
toc     : true
public  : true
parent  : [[Github-actions]]
latex   : false
---
* TOC
{:toc}

# Github Packages

Github Packages는 패키지, 의존성, 컨테이너 등을 호스팅하고 관리하는 깃헙의 서비스다. 코드 저장소로 시작해서 의존성 관리 그리고 actions까지 Devops에 필요한 기능들이 모두 Github에 탑재되고 있다. 이런 Github의 방향성은 [공식 문서](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages)에서 볼 수 있다.

> You can integrate GitHub Packages with GitHub APIs, GitHub Actions, and webhooks to create an end-to-end DevOps workflow that includes your code, CI, and deployment solutions.

Actions랑 Packages를 사용해 본 경험을 늦게나마 정리하고 있는데, 그 사이 Copilot도 나오고, 웹 기반 에디터도 나왔다. 이제 Serverless 배포 환경만 제공하면 진짜 end-to-end 워크 플로우가 Github 안에서 이뤄질 것이다. (이미 개발하고 있을지도)

# Gradle Registry 설정

문서화가 잘 되어 있기 때문에 코드 스니펫만 보면 금방 설정이 가능하지만, [예제](https://github.com/pierceh89/maven-package-example)를 만들어 봤다.

## build.gradle.kts

- **`maven-publish` 플러그인을 추가한다.**

```kotlin
plugins {
    id("maven-publish")
}
```

- **package를 publish하기 위한 설정을 추가한다.**

경로는 `https://maven.pkg.github.com/OWNER/REPOSITORY` 로 설정하고, 인증 정보를 넣어줘야 한다. `username`은 id를 넣고 `password`에는 package 등록용 토큰을 넣으면 된다. 커밋 로그에 토큰값이 올라가지 않도록 주의하자.

로컬에서 테스트를 하기 위해 스냅샷을 올리는 경우가 있기 때문에 나는 시스템 환경변수로 토큰 값을 등록해서 사용하고 있다.

토큰은 <https://github.com/settings/tokens> 에서 생성하면 된다.

```kotlin
publishing {
    repositories {
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/pierceh89/maven-package-example")
            credentials {
                username = System.getenv("USERNAME")
                password = System.getenv("TOKEN")
            }
        }
        publications {
            register<MavenPublication>("gpr") {
                from(components["java"])
            }
        }
    }
}
```

이제 `./gradlew publish` 명령어를 통해서 퍼블리시하면 웹에서 스냅샷이 올라간 것을 확인할 수 있다.

![패키지 업로드 상태](https://user-images.githubusercontent.com/12782821/197101103-30623b13-92ae-4931-a563-3bfc41450bb9.png)

## actions 스크립트 추가하여 자동 빌드

SNAPSHOT은 편하게 로컬에서 마음대로 올려도 괜찮지만 RELEASE 버전은 actions를 통해서 올리고 싶다고 가정해보자. (로컬에서 릴리즈 못하도록 강제하는 방법은 없는것 같다.)

Actions의 장점은 남들이 만들어둔 검증된 워크플로우(re-usable workflow)를 누구나 재사용 가능하다는 것이다. 젠킨스와 비교해서 설명해보면 re-usable workflow는플러그인, runner는 빌드를 실제로 수행하는 에이전트/슬래이브라고 할 수 있다. 젠킨스도 플러그인 생태계가 잘 갖춰져있지만 젠킨스의 특정 버전과 플러그인의 버전이 종속되는 등 관리에 어려움이 있었다. 반면에 actions는 self hosted runner나 github이 제공하는 runner나 실행 환경에 차이가 없어보이고(현재까지는), 워크플로우의 경우에 tag나 branch로 사용하는 버전을 고정해둘 수 있기 때문에 관리가 더 편리하다.

Actions 탭에 들어가서 gradle을 입력하면 나오는 `Publish Java Package with Gradle`을 Configure하면 workflow yaml 파일이 생성된다. 

![actions 생성하기](https://user-images.githubusercontent.com/12782821/197116198-d3aa270d-d449-47fd-8c19-a6e66179bda2.png)

이 파일을 생성하고 릴리즈를 만들면 actions가 자동으로 실행되고 패키지가 업로드 된 것을 아까와 마찬가지로 확인할 수 있다.

![actions 실행 로그](https://user-images.githubusercontent.com/12782821/197296174-ea70e6ac-6ae7-4c56-821a-eb4eec0a54d9.png)

[자동 생성된 스크립트](https://github.com/pierceh89/maven-package-example/blob/5422b3674c709ad64613b036ce657a778d5023bc/.github/workflows/gradle-publish.yml)에서 확인할 부분은 두 가지가 있다. 

{% raw %}
```yaml
    ...
    - name: Publish to GitHub Packages
      uses: gradle/gradle-build-action@67421db6bd0bf253fb4bd25b31ebb98943c375e1
      with:
        arguments: publish
      env:
        USERNAME: ${{ github.actor }}
        TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
{% endraw %}

앞서 로컬에서 패키지를 퍼블리시하기 위해서 토큰을 생성해야 한다고 했었다. 그 토큰을 리파지토리 secret에 등록하고, actions에 설정하는 것도 가능하지만 워크플로우를 돌릴 때 사용할 수 있는 `secrets.GITHUB_TOKEN`을 제공한다. `env.USERNAME`, `env.TOKEN`은 해당 스텝에서 사용할 환경변수를 등록하는 것이고, 들어가는 값은 각각 워크플로우를 trigger한 유저 `github.actor`, `secrets.GITHUB_TOKEN`이다. `github`으로 시작하는 값들은 github context라고 하는데 <https://docs.github.com/en/actions/learn-github-actions/contexts#github-context>에서 확인할 수 있다.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
	permissions:
	  contents: read
	  packages: write
```

그리고 `permissions` 구문은 `GITHUB_TOKEN`의 권한을 설정하는 것으로, 리파지토리 읽기, 패키지 쓰기 권한을 설정한다. (디폴트로 들어가는 추가 권한들은 <https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token>서 확인 가능하다.)

# Package 사용하기

패키지를 사용하려면 리파지토리를 추가하고 의존성을 추가해야 한다.

- **리파지토리 추가** 

```kotlin
repositories {
    mavenCentral()
    maven {
        url = uri("https://maven.pkg.github.com/pierceh89/maven-package-example")
        credentials {
            username = System.getenv("USERNAME") 
            password = System.getenv("TOKEN") 
        }
    }
}
```

- **의존성 추가**

```kotlin
dependencies {
    implementation("io.github.pierceh89:maven-package-example:1.0-SNAPSHOT")
}
```

## 사용하는 깃헙 패키지가 여러개인 경우

사용하는 깃헙 패키지가 여러개이고, 각각 저장소가 별도로 있다면 어떻게 해야 할까? repository에 깃헙 저장소를 각각 등록해줘야 한다. 좀 번거롭단 생각이 들었는데 우연히 꼼수(?)를 발견했다. 같은 user(org)의 패키지를 사용하는 경우로 제한되지만 리파지토리 추가 한 번으로 그 user(org)의 모든 패키지를 가져올 수 있다.

maven repository를 등록할 때 쓰는 `https://maven.pkg.github.com/pierceh89/maven-package-example` 주소에서 `maven-package-example` 부분은 예를 들어 `maven`이라는 임의의 경로로 바꿔도 정상적으로 디펜던시를 가져올 수 있다. 내부 구조가 어떤지 모르겠지만 가능하다. 다음 내가 올린 예제 패키지의 메타데이터 파일을 불러오는 코드다. `$TOKEN`에 앞서 발급받은 토큰을 넣으면 경로가 달라도 동일한 결과가 나온다.  아마도 RESTful한 주소를 만들기 위해서 이런 구조의 url이 나온 것 같다.

```sh
# 첫번째 curl과 두번째 curl의 결과가 동일하다.
curl https://maven.pkg.github.com/pierceh89/maven-package-example/io/github/pierceh89/maven-package-example/1.0-SNAPSHOT/maven-metadata.xml -u pierceh89:$TOKEN | xmllint -format -

curl https://maven.pkg.github.com/pierceh89/maven/io/github/pierceh89/maven-package-example/1.0-SNAPSHOT/maven-metadata.xml -u pierceh89:$TOKEN | xmllint -format -
```

