---
layout  : wiki
title   : Spring의 Aspect Oriented Programming(AOP)
summary : AOP 사용기
date    : 2021-01-28 17:52:59 +0900
updated : 2021-02-17 23:09:33 +0900
tag     : spring aop aspect-oriented-programming
toc     : true
public  : true
parent  : [[programming]]
latex   : false
---
* TOC
{:toc}

# 관점 지향 프로그래밍은 왜 나오게 됐나?

Aspect-Oriented-Programming(AOP)은 관점 지향 프로그래밍이라고도 한다. 객체 지향 프로그래밍처럼 프로그래밍 패러다임 중 하나다.

객체 지향 프로그래밍(OOP)은 우리가 구현해야 하는 동작 주체를 '객체'로 정하고 속성과 메소드를 통해 추상화한다. 각 객체가 부여받은 역할만 수행함으로써 코드의 중복을 최소화하고 재사용성을 높인다. 그런데도 불구하고 중복되는 코드가 발생한다. 

대표적인 예가 트랜잭션을 은행 프로그램에 적용하는 예이다. 은행 프로그램에 입출금, 계좌이체, 이자 계산 같은 비즈니스 로직이 있다면 각각의 모듈이 하는 일들은 객체로 분리되어 있을 것이다. 그래서 트랜잭션은 각각의 모듈이 처리하는 일의 사이사이에 중복되어 들어가게 된다.

이런 식의 처리는 다음과 같은 문제점들을 갖는다.

- 중복되는 코드 : 중복되는 코드가 반복해서 나타나면 유지보수가 힘들어진다.
- 지저분한 코드 : 핵심 기능 코드 사이에 다른 코드(Cross-cutting concern)가 들어가게 된다.
- 생산성의 저하 : 위와 반복된 말이지만 다른 로직이 섞여 있어 생산성이 저하된다.
- 재사용성 저하 : 핵심 기능 외에 다른 로직이 있기 때문에 재사용성이 떨어진다.
- 변화의 어려움 : 새로운 요구사항이 전체 부분에 많은 영향을 미치는 경우, 새로운 요구사항을 적용하기 힘들다.

AOP는 이러한 문제점들을 보완하기 위한 패러다임이다.

# AOP에서 사용하는 용어들

- Aspect: AOP에서 사용하는 모듈화의 단위다.
- Join point: 메소드 실행이나 예외 처리 같은 프로그램의 실행 시점을 뜻한다. 스프링 AOP에서 Join point는 항상 메소드 실행을 의미한다.
- Advice: Aspect에 의해 특정 join point에 수행되는 액션을 뜻한다. Advice의 종류는 `Before`, `After`, `Around`가 있다.
- Pointcut: 결정자로 매칭되는 Join point. Point cut은 Point cut 표현식으로 매칭된 Join point에 연결된 Advice가 실행된다.
- Target object: 하나 혹은 두개 이상의 aspect에 의해 advised된 object. Advised object라고도 부른다. 

# 사용 예

## Before

Join point이 메소드가 실행되기 전에 동작하는 Advice

## After

Join point이 메소드가 실행된 뒤에 동작하는 Advice

## Around

메소드의 실행을 감싸서 동작하는 Advice. 메소드의 실행전, 실행후 동작 실행이 가능하고 메소드 실행까지 제어할 수 있다.

# 참조

[Aspect Oriented Programming with Spring](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop)
