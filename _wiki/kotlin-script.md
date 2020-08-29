---
layout  : wiki
title   : kotlin-script
summary : kotlin script engine 사용하기
date    : 2020-07-16T22:46:00+0900
updated : 2020-08-29 15:23:29 +0900
tag     : kotlin-script jsr223
toc     : true
public  : true
parent  : [[programming]]
latex   : false
comment : true
---
* TOC
{:toc}

# kotlin script engine

코틀린 스크립트 엔진을 웹 서버에 띄워서 사용하려고 리서치중

[코틀린 예제](https://github.com/JetBrains/kotlin/tree/master/libraries/examples/kotlin-jsr223-local-example)를 보고 따라서 만들어봤는데 메모리 릭이 발생했다. 뒤에 나오는[코틀린 포럼](https://discuss.kotlinlang.org/t/embeddable-kotlin-compiler-long-term-memory-leak/16653/4)에서 찾은 내용을 보니 `KotlinJsr223JvmLocalScriptEngineFactory`는 **예제일 뿐이고**, 실제 jsr223 구현체는 `kotlin-scripting-jsr223`에 있다고 한다. (혼란스러워)

구글링하다 코틀린 스크립트 예제 [리파지토리](https://github.com/Kotlin/kotlin-script-examples)를 찾았는데, 여긴 정작 `kotlin-scripting-jsr223`을 사용하지 않고있고 `kotlin-scripting-jvm`을 사용하고 있다. 어디까지 뒤져봐야할지 고민이다.

# 문제상황

현재 풀어야 하는 문제는
1. 클라이언트에서 제공하는 스크립트를 해석하여 데이터를 가공해야 한다.
2. 스크립트 엔진이 REPL과 같아서 스크립트를 해석할 때마다 코드캐시가 메모리(Non-heap)에 추가된다.
3. 이 코드 캐시가 쌓이면 OOM이 발생한다.

포럼에서 제시한 솔루션은 
1. 동일한 스크립트에 대해서 캐싱 레이어를 둬서 코드 캐시가 쌓이지 않도록 캐싱 레이어 추가
2. 메모리(코드캐시)가 일정량 쌓인 경우 스크립트 엔진을 초기화

였다. 써보니 메모리 릭이라기 보다 REPL의 특성에 의한 이슈라고 보이긴 하는데, 해당 상황을 재현할 수 있는 테스트 코드를 만들어보기로 했다.






# References
https://discuss.kotlinlang.org/t/embeddable-kotlin-compiler-long-term-memory-leak/16653
https://discuss.kotlinlang.org/t/kotlin-script-host-embedded-script-compilation/16811
