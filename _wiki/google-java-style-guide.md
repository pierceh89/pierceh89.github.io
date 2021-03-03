---
layout  : wiki
title   : 구글 자바 스타일 가이드
summary : 구글 표준 자바 코딩 스타일 가이드 번역
date    : 2021-02-22 23:42:46 +0900
updated : 2021-03-04 00:37:53 +0900
tag     : google java-style style-guide
toc     : true
public  : true
parent  : [[programming]]
latex   : false
---
* TOC
{:toc}

[구글 자바 스타일 가이드](https://google.github.io/styleguide/javaguide.html)를 요약해보자는 생각으로 시작했지만, 번역하다 보니 잘 기록해놓는게 좋겠다는 생각이 들어서 쓰다보니 길어졌다. 알게 모르게 구글 스타일을 따르건 것도 있었고, 기준이 모호해서 내 맘대로 했던 것에 기준을 잡아주는 항목도 있었다. 힘들게 번역했으니 자주 보려고 한다.

# 인트로

이 문서는 본질적으로 보편적으로 따르는 엄격한 규칙에 집중한다. 그리고 분명히 강요할 수 없는 조언을 주는 것은 피한다.

## 용어

- `클래스`는 평범한 클래스, enum 클래스, interface, `@interface` 전부 통용해서 사용한다.
- (클래스의) 멤버는 중첩 클래스, 필드, 메소드, 생성자를 의미한다. 즉, initializer(?)와 주석을 제외한 클래스의 모든 내용이다.
- 주석은 구현에 관한 주석만 의미한다. 문서화 주석(documentation comments)이라는 용어는 사용하지 않고 대신에 보편적인 용어인 *Javadoc*을 사용한다.

# 소스 파일 기초

## 파일이름

소스 파일 이름은 대소문자를 구분하는 그 파일이 가지는 딱 하나의 최상위 클래스의 이름 + `.java` 확장자로 구성된다.

거의 공기와 같은 규칙이다. 코틀린은 파일에 최상위 클래스가 여러 개 들어갈 수 있는데도 왠만하면 이 규칙을 지켜서 코딩한다. 파일 트리에서 클래스를 바로 찾을 수 있어서 좋다.

## 파일 인코딩 (UTF-8)

소스 파일들은 **UTF-8**로 인코딩한다.

## 특수문자

### 공백문자

공백문자는 아스키 문자 (0x20)만 사용한다.

1. 문자열이나 문자 리터럴에 있는 다른 모든 공백 문자는 escape 처리한다.
2. 탭은 들여쓰기로 사용하지 **않는다.**

### 특별한 escape

[특별한 escape 시퀀스](http://docs.oracle.com/javase/tutorial/java/data/characters.html) (`\b`, `\t`, `\n`, `\f`, `\r`, `\"`, `\'`, `\\`) 는 상응하는 octal이나 Unicode escape로 표현하지 않는다.

### 아스키 문자가 아닌 것

유니코드 문자 자체를 쓰거나 동등한 유니코드 escape를 사용할 수 있다. 선택은 어떤 것이 **더 읽기 쉽고 이해하기 쉬운지**에 달려있다.

# 소스 파일 구조

소스 파일은 다음의 순서로 구성된다.

1. 라이선스 또는 카피라이트 정보 (있다면)
2. 패키지 선언
3. 임포트 선언
4. 하나의 최상위 클래스

각 섹션마다 분리하는 빈 줄 1개가 들어간다.

## 패키지 선언

컬럼 제한 (100자)가 적용되지 않는다. 길어도 쭉 이어서 쓴다.

## 임포트 선언

### No wildcard imports

**와일드 카드 임포트는 사용하지 않는다.** (코딩테스트하면.. 다 외우나?)

### No line-wrapping (줄바꿈 없음)

패키지 선언과 마찬가지로 컬럼 제한이 적용되지 않는다. 

### 순서와 여백

1. 모든 정적(static) 임포트는 하나의 블럭이다.
2. 모든 정적 외(non-static) 임포트는 하나의 블럭이다.

둘 다 있는 경우, 두 블럭은 한 줄로 분리한다. 각 블럭의 임포트 된 이름들은 아스키 순서로 정렬한다.

## 클래스 선언

### 클래스 내용의 순서

클래스 내부 구성이 어떤 순서로 나오는지에 따라 이해도에 영향을 크게 미친다. 하지만 단 하나 옳은 방법이 있는 것은 아니고 클래스마다 다른 방식이 있을 수 있다.

요지는 클래스마다 어떤 논리적인 순서가 있어야하고, 누가 물어보면 대답해줄 수 있어야 한다. 예를 들어 단순히 새로운 메소드를 파일 마지막에 추가하는 건 시간 순이기 때문에 논리적인 순서라고 볼 수 없다.

# 포맷팅

용어: '블록형 구성(block-like construct)'는 클래스, 메소드, 또는 생성자의 본문을 뜻한다. 배열의 초기화도 예외적으로 블록형 구성으로 취급할 수 있다.

## 중괄호(braces)

### 중괄호가 필수가 아닌 경우도 사용한다

`if`, `else`, `for`, `do`, `while` 은 본문이 비어있을 때나 한 줄일 때도 사용한다.

### 비어있지않은(Nonempty) 블록 : K & R style

비어있지 않은 블록이나 블록형 구성에는 Kernighan and Ritchie style ("[Egyptian brackets](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html)")을 따른다. enum class에 몇가지 예외가 있다.

- 중괄호 열기 전에 줄 바꾸지 않는다.
- 중괄호 열고 줄 바꾼다.
- 중괄호 닫기 전에 줄 바꾼다.
- 중괄호 닫고 줄 바꾼다. 단, `else` 또는 콤마가 오는 경우는 바꾸지 않는다.

```java
return () -> {
  while (condition()) {
    method();
  }
};
```

### 빈 블록

빈 블록의 경우도 K & R style을 따를 수 있지만 간결함을 위해 `{}` 줄바꿈없이 표현 가능하다. 다중 블록 구문(`if/else`, `try/catch/finally`)의 경우는 K & R style로 맞춰준다.

```java
// This is acceptable
void doNothing() {}

// This is equally acceptable
void doNothingElse() {
}
  
// This is not acceptable: No concise empty blocks in a multi-block statement
try {
doSomething();
} catch (Exception e) {}
```

## 블록 인덴트: +2 스페이스

새로운 블록이나 블록같은 구성이 오픈되는 경우 들여쓰기는 두 칸씩 늘어난다. 블록이 끝나면 들여쓰기 단계가 이전 단계로 돌아간다. 들여쓰기 단계는 블록 전체의 코드와 주석에 적용된다.

## 한 줄에 하나의 statement

Each statement is followed by a line break.

## 컬럼 제한 100자 

한 줄에 100자 제한이 있다. 아래 예외를 제외하곤 100자를 넘는 경우 줄바꿈을 해야한다.

예외:

1. 컬럼 제한을 따를 수 없는 경우 (Javadoc 안에 들어가는 긴 URL, 긴 JSNI 메소드 참조)
2. `package` & `import` 구문
3. 주석란의 shell 에 복붙할 수 있는 커맨드

## 줄 바꿈(Line-wrapping)

용어 설명: 코드가 실제로는 한 줄이지만 여러 줄에 걸쳐있는 경우를 *line-wrapping* 이라고 한다.

모든 경우를 설명할 수 있는 포괄적인 공식이 없다. 

### 어디서 끊어야 하는가

원칙은 더 높은 문법 레벨에서 끊는 것이다.

1. 비할당 연산자의 경우, 연산자의 앞에서 줄을 바꾼다.
2. 할당 연산자의 경우, 주로 그 심볼 다음에 줄을 바꾼다. (`foreach` 구문에서 콜론 다음에도 마찬가지)
3. 생성자나 메소드 이름 다음에 오는 괄호 `(`는 줄을 바꾸지 않는다.
4. 콤마(`,`)는 선행하는 토큰과 붙여둔다.
5. 람다에서는 화살표 주위로 줄을 바꾸지 않는다. 단, 람다의 바디가 중괄호가 없고 한 줄이라면 화살표 바로 뒤에서 줄 바꿈이 가능하다.

```java
MyLambda<String, Long, Object> lambda =
    (String label, Long value, Object obj) -> {
        ...
    };

Predicate<String> predicate = str ->
    longExpressionInvolving(str);
```

### 인덴트 연속 최소 +4 스페이스

줄 바꿈이 연속될 때는 원래 라인에서 최소 +4 스페이스를 해줘야 한다.

## 공백 문자 

### 수직 공백

연속되는 멤버나 클래스 초기화(필드, 생성자, 메소드, 중첩 클래스, 정적 초기화, 인스턴스 초기화)의 사이에 1줄씩 띄어준다.

예외1: 연속적인 두 필드 사이에 빈 줄은 옵션이다. 논리적 그룹핑이 필요할 때 해준다.

빈 줄은 가독성을 높이기 위해서 어디에나 들어갈 수 있다. 여러 줄 띄우는건 허용되지만 권장하지는 않는다.

### 수평 공백

언어 또는 다른 스타일 규칙이 요구하는 것, 리터럴, 코멘트, Javadoc를 제외하고 다음에서는 하나의 아스키 공백문자를 사용한다.

1. `if`, `for`, `catch` 등의 예약어를 괄호`(` 와 분리
2. `else`, `catch` 같은 예약어를 중괄호`}`와 분리
3. 중괄호`{`를 열기 전에
  - `@SomeAnnotation({a, b})` 예외, 공백문자 없음
  - `String[][] x = {% raw %}{{"foo"}}{% endraw %};` (`{% raw %}{{{% endraw %}` 사이에 공백문자 없음)
4. 이항 연산자나 삼항 연산자의 양쪽에 공백문자. 다음의 "연산자같은(operator-like)" 심볼 다음에도 적용
  - 타입을 결합하는 `&` ampersand `<T extends Foo & Bar>`
  - 여러 개의 예외를 다루는 catch 블록의 `|` 파이프 `catch (FooException | BarException e)`
  - foreach statement의 `:` 콜론 `for (Item item : itemList)`
  - 람다 표현식에서 화살표 `(String str) -> str.length()`
  아래 두 경우는 띄우지 않는다.
  - `::` 메소드 레퍼런스 `Object::toString`
  - `.` `object.toString()`
5. 캐스팅의 닫는 괄호`)` 다음 또는 `,:;` 다음
6. 라인 끝나는 곳에 코멘트 시작하는 `//` 양쪽에
7. 타입과 변수 선언 사이에 `List<String> list`
8. (선택사항) 배열 초기화할 때 중괄호 사이 `new int[] {5, 6}` 또는 `new int[] { 5, 6 }` 둘다 가능
9. 타입 어노테이션 가운데와 `[]` 또는 `...`

### 수평 정렬: 필요하지 않음

용어 설명: 수평 정렬은 여백을 추가로 넣어서 특정 토큰의 열을 맞추는 일이다.

허용은 되지만 구글 스타일에서 필요하지 않다. 그리고 이미 적용된 곳에 수평 정렬을 유지하는 것도 필요하지 않다.

예제

```java
private int x; // this is fine
private Color color; // this too

private int   x;      // permitted, but future edits
private Color color;  // may leave it unaligned
```

## 그룹핑 괄호: 권장함

선택적으로 그룹핑하는 괄호는 코드 작성자와 리뷰어가 괄호 없이도 잘못 해석될 일이 없다고 동의하면 제거할 수 있다. 모든 코드 독자가 자바 연산자 우선순위 테이블을 암기하고 있다고 가정하는 것은 이성적이지 않다.

## 특정 구조

### Enum 클래스

Enum 클래스도 클래스기 때문에 클래스에 적용되는 다른 규칙들이 마찬가지로 적용된다.

enum 상수 뒤에 오는 콤마 다음에 줄을 띄우는 것은 선택사항이고 추가적으로 빈 줄을 넣는 것도 선택할 수 있다.

```java
private enum Answer {
  YES {
    @Override public String toString() {
      return "yes";
    }
  },

  NO,
  MAYBE
}
```

메소드 없고 도큐멘테이션 없는 경우 배열 초기화처럼 구성할 수 있다.

```java
private enum Suit { CLUBS, HEARTS, SPADES, DIAMONDS }
```

### 변수 선언

#### 변수 선언은 한번에 하나씩만 한다.

`int a, b;` 안된다.

`for` 루프 헤더에 변수 여러개 선언하는 것은 예외로 가능하다.

#### 필요할 때 선언한다.

지역변수를 블록이 시작하는 지점에 선언하지 말고 사용하는 지점에서 가장 가까운 곳에 선언한다.

### 배열

#### 배열 초기화는 "블록 같은" 구조로 만들 수 있다.

```java
new int[] {           new int[] {
  0, 1, 2, 3            0,
}                       1,
                        2,
new int[] {             3,
  0, 1,               }
  2, 3
}                     new int[]
                          {0, 1, 2, 3}
```

#### C 스타일로 배열 선언하지 않는다.

`String[] args` 가능, `String args[]` 안됨.

### switch 구문

용어 설명: 스위치 블록 안에는 하나 혹은 그 이상의 구문 그룹(*statement group*)이 있다. 각 그룹은 하나 또는 그 이상의 스위치 라벨(`case FOO:` 또는 `case default`)이 있고 다음에 하나 또는 여러개의 구문이 나온다.

#### 여백

스위치 블록 내부에 +2, 스위치 라벨 다음에 +2

#### Fall-through: 주석 달기

각 스위치 구문은 `break`, `continue`, `return`에 의해 종료되거나, 혹은 다음 구문 그룹으로 진행되는 것을 주석으로 표시해야 한다. 이 주석은 마지막 구문 그룹에는 필요 없다.

```java
switch (input) {
  case 1:
  case 2:
    prepareOneOrTwo();
    // fall through
  case 3:
    handleOneTwoOrThree();
    break;
  default:
    handleLargeNumber(input);
}
```

#### `default` 는 필수

스위치 구문은 `default` 구문이 아무 코드가 없더라도 꼭 포함해야한다. 예외로 enum 타입의 모든 케이스를 커버하는 경우에는 `default`가 필요없다.

### 어노테이션

한 줄에 어노테이션 하나씩.
```java
@Override
@Nullable
public String getNameIfPresent() { ... }
```

**예외**: 파라미터 없는 어노테이션 하나만 있는 경우, 함수 시그니처 앞에 올 수도 있다.
```java
@Override public int hashCode() { ... }
```

필드에 어노테이션 붙이는 경우 여러개가 한 줄에 올 수도 있다.
```java
@Partial @Mock DataLoader loader;
```

### 코멘트

#### 블록 코멘트 스타일

블록 코멘트에서 `*` 가 앞에 와야한다.
```java
/*
 * This is          // And so           /* Or you can
 * okay.            // is this.          * even do this. */
 */
```

### 제어자(Modifiers)

자바 언어 스펙이 추천하는 순서대로 나와야 한다.
```java
public protected private abstract default static final transient volatile synchronized native strictfp
```

### 숫자 리터럴

`long` 값은 숫자 뒤에 `L`을 붙인다. (1과 혼동을 막기 위해서 꼭 대문자로 쓴다)

# 네이밍

## 모든 식별자에 공통적인 규칙

식별자는 오직 아스키 문자와 숫자 그리고 아래에 제시한 경우에 대해 언더스코어 `_`를 사용한다. 따라서 각 유효한 식별자는 정규식 `\w+`로 매치된다.

구글 스타일에서는 특별한 접두어나 접미어를 사용하지 않는다. 예를 들어 다음은 구글 스타일이 아니다. `name_`, `mName`, `s_name`, `kName`

## 식별자 타입에 따른 규칙

### 패키지 이름

패키지 이름은 전부 소문자이고 연속된 단어들이 이어져 나온다.

예) `com.example.deepspace`

아님) `com.example.deepSpace`, `com.example.deep_space`

### 클래스 이름

클래스 이름은 UpperCamelCase로 쓴다. (첫 단어 시작은 대문자로)

클래스 이름은 보통 명사나 명사구로 쓰고 인터페이스의 경우는 형용사나 부사구로 쓰는 경우가 있다.

어노테이션에 대해서는 특별한 규칙이나 컨벤션이 없다.

테스트 클래스는 테스트하는 클래스의 이름 뒤에 `Test`를 붙여서 만든다.

### 메소드 이름

메소드 이름은 lowerCamelCase로 쓴다.

메소드 이름은 주로 동사나 동사구로 쓴다.

언더스코어는 테스트 메소드 이름에 쓸 수 있는데, 테스트 하는 메소드를 논리적인 부분으로 나눌 때 `<methodUnderTest>_<state>` 패턴으로 쓸 수 있다.

### 상수 이름

상수는 전부 대문자로, 각 단어를 언더스코어로 분리한 형태로 쓴다. `CONSTANT_CASE`

무엇을 상수라고 부를 수 있을까? 상수는 `static final` 필드이고 내용이 **deeply immutable** 하면서 메소드가 사이드 이펙트가 없는 경우를 말한다. (
Constants are static final fields whose contents are deeply immutable and whose methods have no detectable side effects.)

```java
// Constants
static final int NUMBER = 5;
static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");
static final ImmutableMap<String, Integer> AGES = ImmutableMap.of("Ed", 35, "Ann", 32);
static final Joiner COMMA_JOINER = Joiner.on(','); // because Joiner is immutable
static final SomeMutableType[] EMPTY_ARRAY = {};
enum SomeEnum { ENUM_CONSTANT }

// Not constants
static String nonFinal = "non-final";
final String nonStatic = "non-static";
static final Set<String> mutableCollection = new HashSet<String>();
static final ImmutableSet<SomeMutableType> mutableElements = ImmutableSet.of(mutable);
static final ImmutableMap<String, SomeMutableType> mutableValues =
    ImmutableMap.of("Ed", mutableInstance, "Ann", mutableInstance2);
static final Logger logger = Logger.getLogger(MyClass.getName());
static final String[] nonEmptyArray = {"these", "can", "change"};
```

### 상수가 아닌 필드 이름

lowerCamelCase 사용. 주로 명사이거나 명사구로 짓는다.

### 파라미터 이름

lowerCamelCase 사용. `public` 메소드의 파라미터를 한글자로 하는 것은 지양한다.

### 지역 변수 이름

lowerCamelCase 사용

### 타입 변수 이름

두 스타일 중 하나로 사용한다.

1. 대문자 1글자
2. 클래스 이름 + `T` 예) `RequestT`, `FooBarT`

## CamelCase

구글 스타일은 정해진 규칙에 따라서 영어 구를 CamelCase로 변환한다.

1. apostrophe 제거하고 ASCII 코드로 변환한다.
2. 결과를 스페이스나 구두점에 따라 단어들로 나눈다.
3. 줄임말을 포함해서 모두 소문자로 변환한 뒤에 다음 첫글자들을 대문자로 바꾼다.
  - 각 단어의 첫 글자
  - 첫 단어를 제외하고 각 단어의 첫글자 (lower camel case)
4. 마지막으로 각 단어를 붙여서 하나의 식별자를 만든다.

변환 대상 | 바른 예 | 바르지 않은 예
----------|---------|---------------
XML HTTP request | XmlHttpRequest | XMLHTTPRequest
new customer ID | newCustomerId | newCustomerID
inner stopwatch | innerStopwatch | innerStopWatch
supports IPv6 on iOS? | supportsIpv60OnIos | supportsIPv6OnIOS
YouTube importer | YouTuberImporter, YoutubeImporter (가능하지만, 추천하지않음) |

모호하게 하이픈(-)이 들어간 영어단어의 경우 둘 다 가능하다. 예를 들어 nonempty, non-empty 둘 다 맞기 때문에 `checkNonempty`, `checkNonEmpty` 둘 다 가능하다.


# 프로그래밍 관행

## `@Override`는 항상 사용

부모클래스의 메소드를 오버라이딩할 때, 인터페이스의 메소드를 구현할 때, 부모 인터페이스의 메소드를 재정의 할 때 등 적법한 경우에 모두 `@Override` 어노테이션을 붙인다.

예외: 부모 메소드가 `@Deprecated`인 경우

## 잡은 예외: 무시하지 않는다

catch 블록에서 exception을 잡은 경우 예외를 제외하고는 옳지 않다. 정말 catch 블록에서 아무 일도 하지 않는게 타당하다면 주석에 이유를 남겨놓는다.

```java
try {
  int i = Integer.parseInt(response);
  return handleNumericResponse(i);
} catch (NumberFormatException ok) {
  // it's not numeric; that's fine, just continue
}
return handleTextResponse(response);
```

예외: 테스트의 경우 catch 블록에서 잡은 예외의 이름이 *expected* 이거나 시작한다면 주석없이 무시할 수 있다.

```java
try {
  emptyStack.pop();
  fail();
} catch (NoSuchElementException expected) {
}
```

## 정적 멤버: 클래스 이름으로 참조한다.

정적 클래스 멤버를 참조하는 경우 클래스 이름을 통해서 한다.

```java
Foo aFoo = ...;
Foo.aStaticMethod(); // good
aFoo.aStaticMethod(); // bad
somethingThatYieldsAFoo().aStaticMethod(); // very bad
```

## Finalizers: 사용하지 않는다.

`Object.finalize`를 오버라이딩하는 경우는 극히 드물다.

팁: 하지마라. 꼭 해야한다면 [Effective Java Item 7](http://books.google.com/books?isbn=8131726592) 을 읽어보고, 주의깊게 읽어보고 하지마라.

# Javadoc

## 포맷팅

### 일반적인 형태

기본적인 Javadoc 블록의 포맷은 아래 예제와 같다.

```java
/**
 * Multiple lines of Javadoc text are written here,
 * wrapped normally...
 */
public int method(String p1) { ... }
```

혹은 한줄 짜리는 아래와 같다.

```java
/** An especially short bit of Javadoc. */
```

### 단락

각 단락은 줄 시작마다 있는 asterisk(`*`) 만 포함하는 빈 줄 1개로 구분한다. 가장 처음에 오는 블록은 `<p>`로 시작한다.

### 블록 태그

표준적으로 사용되는 "블록 태그"들은 `@param`, `@return`, `@throws`, `@deprecated` 순서로 나온다. 그리고 이 네 개의 타입은 설명 없이는 나오지 않는다. 블록 태그가 한 줄안에 들어가지 않으면 다음에 이어지는 줄은 `@` 위치에서 네칸 띄워서 표시한다.

## 요약 조각(Fragment)

각 Javadoc 블록은 간략한 요약 조각으로 시작한다. 완전한 문장이 아니고 명사 구나 동사 구로 표현한다. `A {@code Foo} is a...` 또는 `This method returns...` 혹은 `Save the record.` 이런식으로 만들지 않는다. 하지만 fragment는 완전한 문장인 것처럼 대문자로 시작해야하고 구두점을 찍어야한다.

팁: 흔히 저지르는 실수인데 `/** @return the customer ID */`는 잘못된 방식이고 `/** Returns the customer ID. */` 이렇게 바꿔야 한다.

## Javadoc 을 사용하는 곳

최소한 Javadoc은 모든 `public` 클래스, 그러한 클래스의 `public` 또는 `protected` 멤버에 대해 있어야 한다. (몇가지 예외를 제외하고)

### 예외: 자기 스스로 설명하는 메소드

간단하거나, 분명한 메소드에 대해서는 Javadoc이 선택사항이다.

### 예외: 오버라이딩

Javadoc은 부모타입 메소드를 오버라이딩 하는 경우에는 항상 있는 것은 아니다.

### Non-required Javadoc

구현 주석이 전반적인 목적 또는 클래스나 멤버의 행위를 정의하는 경우 그 주석은 Javadoc으로 쓴다.

Non-required Javadoc은 위의 규칙들을 엄격하게 따를 필요는 없지만 당연히 권장한다.
