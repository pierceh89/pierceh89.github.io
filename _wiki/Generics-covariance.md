---
layout  : wiki
title   : 제네릭의 변성
summary : 자바, 코틀린이 타입 안전성을 어떻게 보장하는지 알아보자
date    : 2021-01-13 11:42:23 +0900
updated : 2021-01-13 13:32:07 +0900
tag     : java kotlin covariant contravariant 공변 반공변
toc     : true
public  : true
parent  : [[programming]]
latex   : false
---
* TOC
{:toc}

# 제네릭(Generics)

제네릭은 자바 1.5 부터 추가된 기능이다. 클래스와 인터페이스의 매개변수 또는 함수의 매개변수와 반환 타입을 미리 확정하지 않고 정의한 후에 사용되는 시점에서 특정 타입을 지정할 수 있게 해준다. 1.5 버전 이전의 자바에서 컬렉션을 사용하는 경우 컬렉션의 요소는 `Object`로 저장하고 읽기 때문에 타입 안정성을 보장할 수 없었다. 제네릭은 컴파일 타임에 제네릭 타입들을 모두 검사해서 타입 안정성을 보장하는 코드를 짤 수 있도록 해준다. 동시에 이전 자바 버전의 호환성을 유지하기 위해 런타임에는 제네릭 타입의 정보가 사라지게 됐다.

# 무공변(Invariant)

![무공변 예제]({{ site.url }}/assets/img/invariance.png)

위 클래스 다이어그램에서 `String`이 `Object`를 상속하므로 다음 코드처럼 서브 타입의 변수인 `foo`를 수퍼 타입의 변수인 `bar`에 할당하는 것이 가능하다.

```java
String foo = "foo";
Object bar = foo;
```

그런데 제네릭 클래스에서는 같은 상속 관계가 성립할까? `String`은 `Object`의 서브 타입이지만, `List<String>`과 `List<Object>`는 서로 전혀 다른 타입이다. 이런 경우 제네릭을 **무공변(invariant)** 하다고 표현한다. 자바의 제네릭은 무공변이다. 만약 제네릭에서 상속관계가 성립하면 어떻게 될까?

```java
List<String> strList = new ArrayList<>(); // 1
List<Object> objList = strList;	// 2
objList.add(1);	// 3
String str = strList.get(0);	// 4
```

상속관계가 성립하면 2번째 줄의 할당이 가능해진다. 그러면 3번째 줄에서 `objList`에 `Object`를 상속하는 다른 서브 타입을 리스트에 추가할 수 있다. 마지막으로 4번째 줄에서 문자열 리스트에서 정수를 가져와서 문자열로 캐스팅을 시도하면서 `ClassCastException`이 발생하게 된다. 즉, 타입 안전성을 런타임에서 못하게 된다. 이런 상황을 방지하기 위해, 2번째 줄에서 컴파일 에러가 발생하도록(두 타입 사이에 상관 관계가 없도록) 만들었다.

타입 안전성을 보장하게 됐지만 불편한 점이 있다. 예를 들어 `Collection` 인터페이스의 `addAll()` 메소드를 보자.

```java
interface Collection<E> ... {
    ...
    void addAll(Collection<E> items);
}	// A

void copyAll(Collection<Object> to, Collection<String> from) {
   to.addAll(from);
}	// B
```

`Collection` 인터페이스의 `addAll()` 메서드 시그니처가 `A`와 같다면 `B`와 같이 타입 안전성이 보장되는 메소드 호출도 허용이 되지 않는 단점이 있다. 그래서 실제 인터페이스는 다음과 같다.

```java
    void addAll(Collection<? extends E> items);
```

# 공변(Covariant)

위 `addAll()` 메서드의 파라미터에서 `? extends E(out E in kotlin)`가 의미하는 것은 제네릭의 타입으로 `E`또는 `E`의 서브 타입이 들어올 수 있다는 뜻이다. `E`의 서브 타입을 허용해도 타입 안전성이 보장되는 이유는 `items`를 "읽기"만 하기 때문이다. 즉, `E`의 서브 타입을 `E`로 읽는 것은 문제가 없다.

하지만 "쓰기"는 문제가 될 수 있는데, 어떤 객체가 `E`의 서브 타입을 충족하는지 알 수 없기 때문이다. 따라서 "쓰기"에 대해서 타입 안전성을 가져갈 수 있는 제약은 다르게 가져가야 한다.

# 반공변(Contravariant)

```java
interface Collection<E> ... {
    ...
	default boolean removeIf(Predicate<? super E> filter) {
	    ...
	}
}
```

`Collection` 인터페이스의 또 다른 함수를 가져왔다. `? super E(in E in kotlin)`가 의미하는 것은 filter의 타입으로 `E`또는 `E`의 수퍼 타입이 들어올 수 있다는 뜻이다. 즉, 사용 할 때는 `E`또는 `E`의 수퍼 타입을 사용 할 때 타입 안전성을 보장할 수 있다는 의미이다. `Collection<String>` 타입의 변수에 대해 `Predicate<String>` 타입의 `filter`를 인자로 넣어주면, `String` 간의 비교가 가능할 것이다. `Predicate<Object>` 타입의 filter를 인자로 넣어주면, `Object`간의 비교가 된다. 하지만 `String`의 서브 타입을 넣는다면 타입 불일치로 비교가 불가능하다.

# 요약

정리하면 제네릭 클래스를 사용한 타입들은 기존의 상속 관계를 따라가지 않는다. 이것은 컴파일 타임에 타입 안전성을 보장하기 위해서이다. 특정 상황에서는 타입 안전성을 보장하면서 제네릭을 사용한 타입 간에도 상속관계를 유지할 수 있는데, 읽는 경우(read, produce)에만 가능하다. 이러한 클래스나 인터페이스를 공변적(covariant)이라고 한다. 반대로, 제네릭 타입 간 상속관계가 거꾸로 되는 경우는 반공변적(contravariant)이라고 한다.

