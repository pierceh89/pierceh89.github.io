---
layout: post
title: 피해갈 수 없는 디버깅을 도와주는 전략서, Effective Debugging
comments: true
tags:
- book review
---

![Effective Debugging cover]({{ site.url }}/assets/img/effective_debugging_1.jpg){:class="scale-with-grid-center"}

애플리케이션 개발에서 디버깅은 뗄 수 없는 과정이다. 단순한 오타에서 숨어있는 로직 오류까지.. 생각만 해도 골치가 아프다. 학부에서 비주얼 스튜디오에서 C언어로 과제를 구현할 땐 디버거를 쓸 줄 몰라서 printf로 변수값을 하나씩 찍고, 손으로 결과를 계산해보면서 디버깅을 했었다. Java, Javascript 등 다른 언어로 개발을 하면 그 언어에 맞는 도구를 이용해서 디버깅을 했었다. 디버깅을 누구에게 배운게 아니라 시간을 많이 허비한 적도 있고, 몰라서 유용한 도구를 적절히 쓰지 못한 경험도 많다. 그래서 이 책이 나와서 너무 반가웠다.  

책은 디버깅 전략과 같은 방법론으로 시작해서, 다양한 디버깅 도구의 사용법, 그리고 프로그래밍 기법, 컴파일 시점, 실행 시점과 같이 개발 단계별로 디버깅 전략을 소개한다. 저자가 디버깅이 굉장히 고된 작업임을 인지하고, 개발자가 온전히 그 작업에 몰입해야 해결할 수 있다는 것을 강조해서 동료애?를 느낄 수 있었다. 책은 차례대로 읽어도 되고 필요한 챕터를 먼저 읽어도 되는데, 후자를 추천한다. 각 아이템을 읽을 때마다 연관된 아이템의 레퍼런스를 잘 남겨놔서 왔다 갔다하면서 읽기에 좋다.  

책의 난이도는 이펙티브 시리즈답게 솔직히 높다. 저자의 개발 경력만큼 다양한 시나리오에 대해 디버깅 전략을 소개하고 있고, 개발하는 환경 또한 다양하다. 서버 개발자 입장에서, 원격 터미널을 통해 이용할 수 있는 범용 도구들을 소개해주는 것은 유용하게 봤다. 그렇지만, 내가 경험해보지 못한 시나리오가 훨씬 많았다. gdb를 사용하거나 어셈블리어 레벨에서 코드를 살펴보는 것은 꽤 생소했다.  

아직 경력이 적어서 이 책의 전부를 이해하진 못했지만, 초보자라도 여전히 이 책에서 얻을 수 있는 것이 많다고 생각한다. 이슈 트래킹 소프트웨어를 사용하고, 어떻게 구글링해야 원하는 결과를 필터링 해낼 수 있는지, 페어 프로그래밍, 리뷰 프로세스 구축, 유닛 테스트 등 버그가 제품에 들어가지 않도록 최대한 방어하는 절차는 누구든지 이해하고 실천할 수 있기 때문이다. 그래서 이 책을 읽은 개발자들이 디버깅하는 상황을 최소한으로 마주하길 바라면서 리뷰를 마친다.  

#이펙티브 디버깅 #Effective Debugging #Effective 시리즈 #Effective series