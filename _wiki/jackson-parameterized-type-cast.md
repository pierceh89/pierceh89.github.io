---
layout  : wiki
title   : Generic type의 jackson deserialiser
summary : type eraser를 극복하자
date    : 2020-07-21 01:01:25 +0900
updated : 2020-07-29 19:02:51 +0900
tag     : jackson kotlin parameterized-type
toc     : true
public  : true
parent  : [[programming]]
latex   : false
---
* TOC
{:toc}

# AbstractDataConverter

각각 다른 주소의 API를 호출하여 결과값을 deserialize 한 객체를 반환하는 클래스들이 여러개 있다.

추상 클래스를 만들어서 반복되는 코드(deserialization)를 묶어서 처리하고 싶은데 의도한 대로 작동하지 않는다.

## 예제

```kotlin
// does not work
abstract class AbstractDataConverter<T> {

    fun convert(jsonStr: String): T {
        return om.readValue(jsonStr, object : TypeReference<T>() {})
    }
}

class FooDataConverter : AbstractDataConverter<Foo>()

val om = jacksonObjectMapper()

// test code
@Test
fun testGenericDataConverter() {
	val fooJson = """
		{
		  "a": "abc",
		  "b": "abc"
		}  
	""".trimIndent()

	val converter = FooDataConverter()
	val ret = converter.convert(fooJson)

	// java.lang.ClassCastException: class java.util.LinkedHashMap cannot be cast to class io.github.pierceh89.type.Foo
	Assert.assertEquals(ret, Foo("abc", "abc"))
}

```

# Type eraser

의도와는 다르게 `java.lang.ClassCastException`이 발생했다. 클래스의 타입 인자로 `Foo`, `Bar`를 줬는데 왜 이런 에러가 발생할까?

자바와 코틀린 제네릭의 타입 인자는 컴파일시에만 존재하고 실행시에는 사라진다. 그래서 jackson에서 타입 정보를 찾을 수 없어서 예외가 발생한 것이다.

해결방법은 간단하다. 상속받는 클래스에서 구체화된 타입 정보를 반환하도록 넘겨주면 된다.

# AbstractDataTypeConverter

상속받는 클래스가 `TypeReference<T>`를 구현하면 각 `typeReference`는 상속받는 타입(`Type`)을 내부에 저장하게 되어 deserialize가 가능하게 된다.

## 예제

```kotlin
abstract class AbstractDataTypeConverter<T> {

    abstract fun type(): TypeReference<T>

    fun convert(jsonStr: String): T {
        return om.readValue(jsonStr, type())
    }

    protected abstract fun doSomething(value: T)

    fun run(jsonStr: String) {
        val parsed = convert(jsonStr)
        doSomething(parsed)
    }
}

class FooDataTypeConverter : AbstractDataTypeConverter<Foo>() {
    private val typeReference = object : TypeReference<Foo>() {}

    override fun type(): TypeReference<Foo> {
        return typeReference
    }

    override fun doSomething(value: Foo) {
        println(value)
    }
}

// test code
@Test
fun testGenericDataTypeConverter() {
	val fooJson = """
		{
		  "a": "abc",
		  "b": "abc"
		}  
	""".trimIndent()

	val converter = FooDataTypeConverter()
	val ret = converter.convert(fooJson)

	Assert.assertEquals(ret, Foo("abc", "abc"))
	converter.run(fooJson)
}
```

# reified 키워드

코틀린에서는 `reified` 키워드를 제공하는데 아래 처럼 사용하면 실행 시점에서도 타입에 대한 정보를 잃지 않고 사용할 수 있다.

그런데 클래스에는 사용할 수 없고 `inline` 함수의 경우에만 사용할 수 있어서 템플릿 메서드 패턴에서 사용할 순 없다.

```kotlin
inline fun <reified T> convert(jsonStr: String): T {
    return om.readValue(jsonStr, T::class.java)
}
```

# 링크

- <https://www.baeldung.com/java-type-erasure>
- [예제코드](https://github.com/pierceh89/jackson-parameterized-type)
