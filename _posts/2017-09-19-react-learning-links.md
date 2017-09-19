---
layout: post
title: React리액트 관련 링크 모음
comments: true
---

프론트엔드 작업을 하게 되면서 리액트를 사용하게 됐다. 회사에 입사할 즈음 잠깐 배운적이 있었는데 그땐 너무 어려워서 못할것 같았는데 지금 보니 생각보다 금방 배웠다. 간단한 상호작용을 하는 [토이앱](https://pierceh89.github.io/pantone-palette/)은 하루만에 만들 수 있었다. 근데 그게 다가 아니고 redux를 배우고, react-router, redux-thunk 같은 미들웨어를 배우고 나니 프로덕션 코드를 읽을 수 있는 정도가 되었다. 아직 모르는게 더 많지만 내가 참고한 순서대로 링크를 정리해서 공유해보려고 한다.  

## Shadow DOMs

Shadow DOM은 리액트가 리액트처럼 작동하는 아이디어를 이해하기 위해 읽어보면 좋다. 아래에 있는 세 개의 문서는 나도 다 읽어보지 못했지만 아직 위시리스트?로 돼있어서 정리해두었다.  
[ShadowDOM-101](https://www.html5rocks.com/ko/tutorials/webcomponents/shadowdom/)  
[ShadowDOM-201](https://www.html5rocks.com/ko/tutorials/webcomponents/shadowdom-201/)  
[ShadowDOM-301](https://www.html5rocks.com/ko/tutorials/webcomponents/shadowdom-301/)  

## 강의

한글로 된 다른 리액트 강의를 찾아보지는 않았지만 여기보다 잘 가르치기 힘들다고 본다. React, Router, Redux, Middleware 차례대로 배우다보면 금방 리액트 앱을 짤 수 있을 실력이 갖춰진다.  
[React tutorials](https://velopert.com/reactjs-tutorials)  
[Redux](https://velopert.com/3365)  
[Redux and middleware](https://velopert.com/3401)  

## Ducks

조금 복잡한 리액트앱을 짜다보면 프로젝트 구조에 대해서 고민을 하게 되는데 그때 읽어보면 좋은 글이다. Ducks 구조로 만들어두면 파일을 찾아보기가 훨씬 편해진다.  
[The Ducks File Structure for Redux](https://medium.com/@scbarrus/the-ducks-file-structure-for-redux-d63c41b7035c): [영문] Ducks구조로 대체한 이유, 핵심 요약.  
[Ducks documentation](https://github.com/erikras/ducks-modular-redux): [영문] Ducks 구조를 제안한 사람의 documentation. ducks 구조의 예제 코드와 ducks 구조로 redux app을 만들 수 있게 도와주는 라이브러리도 소개하고 있다.  
[Ducks documentation 한글판](https://github.com/JisuPark/ducks-modular-redux): 위 글의 한글 번역  

## 기타

따로 묶어두기 애매하지만 봐두면 좋을 것들은 여기에 모아놨다. 아마 강의를 들으면서 리액트에 익숙해지면 여기에 있는 링크들을 마주치게 될 것 같다.
[React-bootstrap](https://react-bootstrap.github.io): [영문] 부트스트랩이 적용된 리액트 컴포넌트  

[PropTypes](https://github.com/facebook/prop-types#prop-types): [영문] props에 대해 type validation을 도와주는 라이브러리  

[Smart and Dumb Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0): [영문] Smart & Dumb component에 대해 redux의 개발자가 정리한 글  

[React Components, Elements, and Instances](https://medium.com/@dan_abramov/react-components-elements-and-instances-90800811f8ca): [영문] Component, Element, Instance에 대해 정리한 글  
[리액트 렌더링과 성능 알아보기](http://meetup.toast.com/posts/110): 리액트의 성능에 관한 글  

[react-actions](https://github.com/reduxactions/redux-actions): [영문] redux 액션의 생성을 더 편하게 도와주는 라이브러리  

[immutable.js](https://facebook.github.io/immutable-js/): [영문] 페이스북에서 개발한 immutable data structure 라이브러리  
[Immutability-helpers](https://facebook.github.io/react/docs/update.html): [영문] immutable.js와 비슷하게 데이터를 삽입,수정,삭제 할 경우 변경된 새로운 객체를 리턴해주는 라이브러리  

[React documentation](https://facebook.github.io/react/): [영문] 리액트 공식 도큐멘테이션  
[Flux pattern](https://facebook.github.io/flux/): [영문] MVC에서 조금 더 발전된 디자인 패턴 flux  
[Complementary tools](https://github.com/facebook/react/wiki/Complementary-Tools): [영문] 페이스북이 알려주는 리액트 개발 보충 도구들  
