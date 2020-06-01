---
layout: post
title: 자바 8을 얼른 적용해보고 싶다면, Java8 in action
summary : 
date    : 2017-07-11 12:33:00 +0900
tag     : 서평 자바8인액션 Java8-in-action In-action-시리즈 In-action-series
toc     : false
comment : true
public  : true
---

![Java8 in action cover]({{ site.url }}/assets/img/java8_in_action_1.jpg){:class="scale-with-grid-center"}

자바 9이 나온지 수개월이 지나가는 시점에 자바 8을 리뷰하고 있는게 좀 우스울수도 있지만, 리뷰를 해보려고 한다. 자바 9이 나온지 아직 시간이 많이 지난것이 아니라서 프로덕션 런타임으로 결정하기엔 시기상조라고 본다. 그래서 지금이라도 자바 8을 살펴보는 사람들에게 이 리뷰가 도움이 됐으면 한다.  

결론부터 말하면, 자바 8을 잘 사용하고 싶은 사람들 모두에게 권하고 싶은 책이다. 책을 처음부터 순서대로 읽으면 잘 짜여진 이야기를 읽는 느낌이 든다.  

1부에서는 컴퓨터의 하드웨어 발전 방향이 변함에 따라, 병렬프로그래밍의 중요성이 커지고, 자바가 함수형 프로그래밍이라는 새로운 패러다임을 받아들이게 된 이유를 차례대로 알 수 있었다.  

2부에는 이어서 스트림 API에 대해 자세히 설명한다. 기존의 명령형 프로그래밍 코드와 스트림으로 리팩토링된 코드를 비교하면서 스트림 API를 통해 명료하고 짧아진 코드를 확인해 볼 수 있다.  

3부에서는 Optional, 디폴트 메서드, CompletableFuture, 새로운 DateTime 라이브러리를 소개한다. 이들 라이브러리는 이전 자바에 비해 훨씬 강력한 기능들을 제공하기 때문에 꼭 봐야한다. 나아가 객체지향에서 자주 쓰이던 디자인 패턴이 함수형 프로그래밍을 사용하면 어떻게 간단히 구현될 수 있는지 보여준다.  

4부에서는 함수형 프로그래밍을 살펴보고, JVM위에서 돌아가는 함수형 프로그래밍 언어 스칼라와 자바를 비교한다. 또 흥미로운 내용을 부록에 넣어두었는데 이것들은 시간이 될 때마다 읽으려고 한다.  

책 전체에 걸쳐서 코딩 퀴즈와 예제가 많기 때문에 하나씩 실제로 따라해본다면 자바의 새로운 기능에 적응하기 쉬울 것이다. CompletableFuture나 LocalDateTime 같은 경우는 기존 라이브러리에 비해 훨씬 좋아졌으니 꼭 실습해보기를 바란다.  

함수형 프로그래밍이 조금 생소했기에 책을 읽는데 시간이 좀 걸렸지만 읽을 가치가 있는 책이었다.
