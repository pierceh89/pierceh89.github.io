---
layout: post
title: Spring Boot 이해하면서 쓰기, '실전 스프링부트 워크북'
comments: true
---

<center>
<img src="{{ site.url }}/assets/img/pro_spring_boot_1.jpg" style="display: inline;" height="359" alt="Pro Spring Boot, 이랬던 책의 표지가" />
<img src="{{ site.url }}/assets/img/스프링부트_워크북_1.jpg" style="display: inline;" width="280" alt="실전 스프링 부트 워크북, 이렇게 바뀌었다." />
</center>


'워크북(workbook)' 이라는 단어의 뜻을 찾아보면 '작업자의 매뉴얼', '연습 문제를 모아놓은 책' 등이 있다. 내가 이 책의 제목을 봤을 땐 쿡북느낌의 필요할 때마다 레시피를 찾아 바로 적용하는 느낌의 책을 연상하게 되었다. 이전에 '스프링부트 공작소'라는 책을 봤는데 그러한 것이어서 이 책도 그러려니 했다. 하지만 책을 표지로만 판단하지 말라는 명언대로, 책을 계속 읽어보니 워크북과는 거리가 멀다는 느낌이 들었다.  
원서의 이름을 찾아보니 [Pro Spring Boot](http://www.apress.com/br/book/9781484214329)였다. 아~ 역시. 책의 대상 독자는 어떨까?

>Who This Book Is For
>
>Experienced Spring and Java developers seeking increased productivity gains and decreased complexity and development time in their applications and software services.

'향상된 생산성, 그리고 복잡도와 개발시간의 절감을 원하는 숙련된 스프링 및 자바 개발자'가 독자이다. 그런데 번역서의 난이도는 초중급이라고 되어있다. 여하간 스프링부트가 코드를 하나도 생성해내지 않고 내부적으로 어떻게 작동하는지, 어떤 옵션을 지원하는지 세세하게 설명하는 동시에, 스프링에 대해서도 복습하는 형식으로 설명을 추가적으로 해주고 있다. 이런 점에서 초보가 봐도 무방한 책이라고 생각했던 것 같다.  

책에서 아쉬운 점은 Maven을 의존성 관리툴로 설명하고 있는 점이다. [소스코드](https://github.com/apress/pro-spring-boot)를 보면 Maven, Gradle 둘 다 사용할 수 있지만 커맨드를 실행할 때는 Maven위주로 설명하고 있다. p85에서 파라미터를 추가하는 테스트가 있는데, 동일한 작동을 시험해보기 위해 gradle 명령어를 찾아보고, 추가적으로 Task를 정의해줘야 했었다. [Stackoverflow 참조](https://stackoverflow.com/questions/23316843/get-command-line-arguments-from-spring-bootrun)  

책에 대해 좀 투덜거리긴 했지만 책의 내용은 전혀 흠잡을 곳이 없다. 스프링 자동구성, 스프링 부트 테스트, 데이터 액세스, 보안, 메시징, 액추에이터, 배포, 클라우드까지 주요한 내용들을 모두 담고있다. 생산성 향상을 위해 스프링부트를 현장에 투입하려는 사람들이 많아진 시점에 잘 나온 책인 것 같다. 추가적으로 사람들이 생산성을 위해 자바가 아니라 코틀린을 베이스로 개발하는 것도 고려하고 있는데, 코틀린 + 스프링부트 보일러 플레이트를 만드는 작업을 해보면 재밌을 것 같다.  

덧. 책이 만약 Gradle을 중심으로 설명했다면 아마 책이 더 얇아졌을것 같다 :)  

#Pro Spring Boot #스프링부트 워크북 #스프링부트 책 추천
