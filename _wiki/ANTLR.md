---
layout  : wiki
title   : ANTLR 이용하여 Reverse Polish Notation을 파싱하고 중위표현법으로 변환하기
summary : ANTLR을 이용한 DSL 만들기
date    : 2020-09-20 15:20:28 +0900
updated : 2020-09-20 16:51:17 +0900
tag     : antlr dsl parser reverse-polish-notation
toc     : true
public  : true
parent  : [[programming]]
latex   : false
---
* TOC
{:toc}

# ANTLR

[ANTLR](https://www.antlr.org/) 은 ANother Tool for Language Recognition의 약자로, dsl을 만들 때 사용할 수 있는 라이브러리다. 

지난번에는 코틀린 언어가 가진 특성을 이용해서 dsl을 만들어봤다. 코틀린 dsl을 이용해 코드를 작성하는 일은 쉽지만, 역으로 문자열을 동적으로 코틀린 dsl로 컴파일하고 실행하기 위해서는 스크립트 엔진이 필요하고 메타스페이스 메모리를 설정해줘야 하는 등 신경써야할 부분이 좀 있었다.

ANTLR을 사용하면 정의한 Lexer와 Parser의 코드가 자동으로 생성되고, Parser를 상속받아 필요한 메소드만 override하면 된다. 앞에서는 코틀린 코드가 동적으로 컴파일되기 때문에 메타스페이스에서 컴파일된 코드가 증가하는 구조이지만, 이 경우는 힙 메모리 내에서 작업이 처리되기 때문에 신경 쓸 일이 적다고 할 수 있다.

성능이나 확장성 또는 유지보수성을 전부 고려해보진 않았지만 스크립트 엔진 튜닝 때문에 삽질한 경험으로는 후자가 더 나아보인다.

# Reverse Polish Notation (RPN)

[Reverse Polish Notation](https://en.wikipedia.org/wiki/Reverse_Polish_notation) 에 대해 처음 접한 것은 폴란드에 교환학생으로 수업을 듣던 때였다. 자료구조였나 알고리즘이었나 수업 시간에 문자열에서 RPN을 해석하고 그 식의 연산 결과를 리턴하는 프로그램을 만들라는 과제를 받았다.

우리가 사용하는 표기법은 중위 표기법(infix notation)이고, RPN은 다른 말로 후위 표기법(postfix notation)이라고 부르기도 하는데, 연산자 우선순위를 표시하기 위해 괄호를 사용할 필요가 없어서 간결하고, 컴퓨터에서도 해석하기 더 쉬운 표기법이라고 한다.

과제를 받았을 땐 C++로 스택을 이용해서 풀었는데 ANTLR을 사용하면 순식간에 풀 수 있다.

아래에서 설명하는 예제의 전체 코드는 [github](https://github.com/pierceh89/reverse-polish-notation) 에서 확인할 수 있다.

## Grammar

컴파일러 수업을 듣지 않아서 이해하는데 애를 먹었지만, [온라인 도큐멘테이션](https://github.com/antlr/antlr4/blob/4.8/doc/index.md) 을 보고 RPN의 문법을 정의했다.

`@header`는 생성되는 파일에 패키지 정보가 들어가도록 해준다. 파일이 생성된 후에도 패키지 정보가 없어서 import 할 때 못찾는다고 나오기 때문에 꼭 넣어줘야 한다.

토큰은 대문자로 시작해야 하고 편의상 전부 대문자로 지었다. `OPER`, `VALUE`, `WS`를 토큰으로 정의했다.

파서는 소문자로 시작해야 하고 여기서는 `expr` 하나만 정의했다.

```antlr
grammar PolishNotation;

@header {
    package io.github.pierceh89.notation;
}

OPER : [+*/-] ;
VALUE : [0-9]+ ;
WS : ' ';

expr:   VALUE
    |   expr WS expr WS OPER;
```

시각화 도구를 이용하면 테스트 하려는 입력에 대해서 어떻게 ParseTree가 생성되는지 확인할 수 있다. 

![3 5 * 에 대한 Parse Tree](https://user-images.githubusercontent.com/12782821/93705671-ffc54180-fb59-11ea-904e-831df379e867.png)



![2 5 + 4 2 + * 에 대한 ParseTree](https://user-images.githubusercontent.com/12782821/93706448-4cf8e180-fb61-11ea-9e76-2b38480dabc7.png)

## Listener, Visitor

그레이들 플러그인을 설정하면 `generateGrammarSource` task가 생기는데 이걸 실행하면 Lexer, Parser가 자동으로 생성된다. `grammar` 이름이 접두사로 붙는 파일들이 생기는데 `PolishNotationBaseListener` 또는 `PolishNotationBaseVisitor`를 상속받아서 함수를 재정의하면 된다.

visitor의 경우 `@Override public T visitExpr(PolishNotationParser.ExprContext ctx) { return visitChildren(ctx); }` ParseTree를 순회하면서 객체를 리턴해줘야 한다.

listener의 경우 `@Override public void enterExpr(PolishNotationParser.ExprContext ctx) { }`, `@Override public void exitExpr(PolishNotationParser.ExprContext ctx) { }` 리턴 타입은 void 이다.

visitor와 listener 둘 다 ParseTree의 루트부터 아래로 내려가면서 방문하는 것은 동일하지만, visitor는 재귀적인 구현이 가능하고 listener는 expr에 대해서 위의 메소드 호출이 반복적으로 일어나기 때문에 구현하는 사용자가 상태를 저장하고 있어야 한다.

visitor의 구현은 아래 코드가 전부다. 

```java
public class PolishNotationConverter extends PolishNotationBaseVisitor<Expression> {

    public Expression convertExpression(String polishExpression) { ... }

    @Override
    public Expression visitExpr(PolishNotationParser.ExprContext ctx) {
        if (ctx.expr().isEmpty()) {
            return new ValueExpression(Integer.parseInt(ctx.VALUE().getText()));
        } else {
            String operator = ctx.OPER().getText();
            boolean paren = false;
            if (operator.equals("*") || operator.equals("/")) {
                paren = true;
            }
            return new MultiExpression(visitExpr(ctx.expr(0)), visitExpr(ctx.expr(1)), operator, paren);
        }
    }
}
```

## Test

`Expression`의 클래스를 구현해서 `toString()`을 override하고 `PolishNotationConverter`를 구현하는 것으로 간단하게 RPN을 파싱하고 중위 표기법으로 변환해주는 코드를 완성했다.

```java
@Test
public void testExpressions() {
	System.out.println(sut.convertExpression("1 2 +"));				// 1 + 2
	System.out.println(sut.convertExpression("2 3 + 4 *"));			// (2 + 3) * (4)
	System.out.println(sut.convertExpression("3 5 + 4 2 + *"));		// (3 + 5) * (4 + 2)
}
```

