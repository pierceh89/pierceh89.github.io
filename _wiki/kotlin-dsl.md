---
layout  : wiki
title   : kotlin-dsl
summary : kotlin 으로 dsl 만들어 활용하기
date    : 2020-06-03T09:47:23+0900
updated : 2021-03-06 16:27:16 +0900
tag     : kotlin dsl
toc     : true
public  : true
parent  : [[programming]]
latex   : false
comment : true
---
* TOC
{:toc}

kotlin을 이용해서 dsl을 만들때 알아야할 필수적인 것들

## 확장함수
기존 클래스를 변형하지 않고 함수를 만들어 기능을 추가할 수 있다. 
라이브러리를 사용하면서 추가하고싶은 기능이 있을 때 유용하다. 

```kotlin
import java.time.LocalTime

val ampmFormatter: DateTimeFormatter.ofPattern("a")
fun LocalTime.getAmpm(): String = this.format(ampmFormatter)

val now = LocalTime.now()
// "AM" or "PM"
now.getAmpm()
```

## 중위호출
호출하는 메소드의 인자가 하나일 때 .method(param)이 아닌 method param 으로 띄어서 호출할 수 있다. 
자연어처럼 읽을 수 있는 dsl을 만들 때 유용하다
```kotlin
class Member(val name: String)

object new {
	infix fun member(val name: String): Member = Member(name)
}
val member = new member "diego" // Member(name="diego") 생성
```

## 수신객체지정 람다
apply, let을 사용할 때를 생각해보면 된다.
apply { } 내부에서 메소드를 사용할 때 this를 붙이지 않아도 되는데 동일하게 사용할 수 있다.
예제를 보면 큰 차이가 없어 보이지만 dsl을 설계할 때 코드가 깔끔해보이는데 큰 역할을 한다. 
```kotlin
// 수신객체 지정하지 않은 람다
fun buildString(action: (StringBuilder) -> Unit): String {
    val sb = StringBuilder()
	action(sb)
	return sb.toString()
}
println(buildString { it.append("abc") }
// "abc"

// 수신객체를 지정한 람다
fun buildString(action: StringBuilder.() -> Unit): String {
    val sb = StringBuilder()
	sb.action()
	return sb.toString()
}
println(buildString { append("abc") }
// "abc"
```

## invoke
인스턴스를 함수처럼 호출하는데 사용할 수 있다.
그렇게 사용하기 위해서 invoke라는 이름의 메소드를 정의하면 되는데 **operator** 키워드를 붙여줘야 한다.

```kotlin
class Echo(val text: String) {
	operator fun invoke() { println(text) }
}

val echo = Echo("Hello World")
echo()
// Hello World
```

