---
layout: post
title: 윈도우에서 Git + IntelliJ 개발환경 설정 방법
summary : 
date    : 2019-04-12 13:04:00 +0900
tag     : 개발환경설정
toc     : false
comment : true
public  : true
---

이제 막 자바를 통해 프로그래밍을 접하는 사람들을 위해 윈도우즈에서 개발환경을 설정하는 방법을 알려드리고자 합니다.  

# Git 설치
소스 코드의 버전 관리를 위해 필요합니다.  
IntelliJ에서는 Git을 통합한 개발환경을 제공하지만 설치는 따로 해줘야합니다.  
[링크](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%84%A4%EC%B9%98)에 들어간뒤, 'Windows에 설치' 항목에서 Windows용 Git을 다운받아서 설치합니다.  

![Git install]({{site.url}}/assets/img/git_install.png)

# JAVA(JDK)
자바로 개발을 하기 위해서는 Java Development Kit(JDK)가 필요합니다. 상업적 용도가 아니라면 Oracle JDK든 Open JDK든 상관없기 때문에 Oracle JDK를 받아봅시다. [링크](https://www.oracle.com/technetwork/java/javase/downloads/index.html)에 들어가서 Java SE 8u201 / Java SE 8u202를 찾아서 JDK download를 클릭합니다.  

![Oracle JDK]({{site.url}}/assets/img/jdk_download.png)

라이선스 동의를 클릭하고 Windows x64용 파일을 다운로드하여 설치합니다.  

![Java SE 8u201]({{site.url}}/assets/img/se8.png)

설치가 끝나면 환경변수를 설정해줍니다. 제어판 -> 시스템 -> 고급 시스템 설정에서 [고급] 탭에서 환경 변수를 등록합니다.  

![환경변수 설정]({{site.url}}/assets/img/env_var1.png)

시스템 변수를 등록하고  

![JAVA_HOME 설정]({{site.url}}/assets/img/java_home.png)

Path에 `;%JAVA_HOME%\bin` 을 등록해주면 됩니다. 세미 콜론은 앞의 path와 구분하기 위해 붙입니다.

![Path에 등록]({{site.url}}/assets/img/java_path.png)

여기까지 설정을 하고 나면 명령 프롬프트(커맨드)를 열어서 자바, 자바컴파일러가 path에 등록됐는지 확인합니다.
```
java -version
javac -version
```

마지막으로 IntelliJ Idea를 다운받아서 설치해주면 개발 환경 설정이 끝납니다.  
라이선스가 있다면 Ultimate 버전을 받으면 되고, 없다면 Community 버전을 받아도 공부를 시작하기에 문제 없습니다.  
[IntelliJ 링크](https://www.jetbrains.com/idea/)
