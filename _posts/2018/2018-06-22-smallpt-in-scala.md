---
layout: post
title: Smallpt in scala
summary : 
date    : 2018-06-22 13:03:32 +0900
tag     : computer-graphics rendering
toc     : false
comment : true
public  : true
---

![Sampling 300]({{site.url}}/assets/img/smallpt.png)

[Smallpt](http://www.kevinbeason.com/smallpt/)는 Global Illumination Renderer다. 과제하면서 잘 이해안되는 코드를 억지로 만들어냈던 기억이 난다. 회사 다니기 시작한 이후로는 그래픽 쪽으로는 볼 시간이 거의 없었다. 초기에 스칼라를 막 배우고나서 포팅하려는 시도를 한번 했었는데 그땐 expression이나 함수형 언어에 대한 개념이 전혀 없어서 해내지 못했다.  

이대로 놔두기 아쉬워서 지금 다시 해보니 의외로 하루 만에 코드를 옮기는 작업이 끝났다! `val`를 쓸지 `var`를 쓸지 정하거나, 함수 파라미터의 레퍼런스를 어떤 식으로 변형하는가, 그리고 if문에서 return이 함수의 리턴이 아닌 것을 어떻게 옮겨야 하는가가 고민스러운 부분이긴 했다. 아쉬운데로 먼저 작동하는 코드로 구현하기로 결정했다.  

그런 다음 해야할 작업은 '스칼라답게' 코드를 변형하는 작업인데, 아직 진행중이다. 일부 리팩토링한 코드가 있는데 튜플을 사용해서 변경했다. 이미지 프로세싱에서 가장 많이 보는 루프가 아마 x 좌표 루프, y 좌표 루프가 차례로 나오는 것일텐데 이 이중루프를 튜플을 이용해 하나로 바꿀 수 있다.  

```scala
// first version
for (x <- 0 until w) {
	for (y <- 0 until h) {
		// do something
	}
}

// tuple version
val canvas = for { x <- 0 until w; y <- 0 until h } yield (x,y)
canvas.foreach( pixel -> {
	//do something
})
```

for loop을 먼저 C++와 같은 방식으로 구현한 다음, 튜플로 (x, y)좌표를 만들어 대체했다. 이렇게 사용하면 `par`만 추가하면 병렬 처리도 할 수 있고 가독성도 더 좋아진다.  

그 뒤엔 subsampling해서 계산한 radiance를 모으는 루프를 `foldLeft`를 사용해 대체했다.

```scala
var sum = 0
for (i <- 1 to 10) {
	sum += i
}

val sum = (1 to 10).foldLeft(0)((acc, i) => acc + i)
```

여기서도 마찬가지로 기존 명령형 코드로 구현한 다음에 함수형으로 리팩토링을 했다. 어떤 작업을 하는지 명료해지기도 하고 픽셀마다 radiance를 0으로 초기화하는 과정을 잊는 등의 실수를 할 가능성도 적어진다.  

위에서 본 예제들은 어떻게 처리해야 할지 보이는 코드였다면, 나머지 부분은 잘 보이지 않는다.. 특히 tail recursion을 구현하는 일은 radiance 계산을 어느 정도 이해하지 않으면 할 수 없을 것 같다.(다 잊어버렸다..) Smallpt를 line by line으로 설명한 [슬라이드](http://www.kevinbeason.com/smallpt/#moreinfo)를 보면서 이해해보고 얼른 마무리 해야겠다.  

### Link
[Smallpt](http://www.kevinbeason.com/smallpt/)  
[Smallpt-scala](https://github.com/pierceh89/smallpt-scala)
