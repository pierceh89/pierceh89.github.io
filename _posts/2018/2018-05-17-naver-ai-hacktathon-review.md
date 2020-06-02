---
layout: post
title: 네이버 AI 해커톤 2018 참여 후기
summary : 
date    : 2018-05-17
tag     : nlp nsml hackathon
toc     : false
comment : true
public  : true
---

해커톤이 끝난지 어느새 한달이 되어가서 더 뻘쭘해지기 전에 올려보려고 한다.  

페이스북 [TensorFlow KR](https://www.facebook.com/groups/TensorFlowKR/?ref=bookmarks)를 팔로우하면서 해커톤에 대해 알게 됐는데, 그곳에서는 거의 매일 사람들이 활발하게 연구하고 공유하는 모습을 볼 수 있다. 그래서 솔직히 신청해도 내가 1라운드를 통과할 수 있을까(아니 참가 명단에 들어갈 수 있을까)하는 생각을 했었다.  

또 마지막 결선 일정이 마침 내가 여행을 계획해둔 날이랑 겹쳐서(...) 아예 신청을 안하려했지만, 설마 내가 결선까지 가겠어 하는 마음으로 신청했다. (그리고 결선 참가 커트라인에 들어갔다.)  

# nsml과 첫만남
[nsml](https://github.com/naver/ai-hackathon-2018/blob/master/FAQ.md)은 네이버에서 개발한 머신러닝 클라우드 플랫폼이고, 이번 해커톤에서 사용됐다. 파이썬 기반 머신러닝 라이브러리는 대부분 사용할 수 있고, 추가적으로 더 필요한 라이브러리는 해당 라이브러리를 설치한 도커 이미지를 사용하면 된다. 나는 gensim, twitter tokenizer(스칼라로 작성됐고, [KoNLPy](http://konlpy.org/en/v0.4.4/) 파이썬 인터페이스가 있다)를 사용하기 위해 [이미지](https://hub.docker.com/r/pierceh89/nsml_twitter/)를 따로 만들어서 사용했다.  

처음에 테스트 삼아 베이스코드를 실행해서 학습하고 제출해봤다. cli가 익숙하지 않더라도 학습(run), 제출(submit), 진행 상황(ps), 모델 확인(model), 로그(logs) 이 정도 키워드만 쓰면 nsml는 다 배운 것이다. 참 간단했다.

# 모델 적용
처음 계획은 여러가지 모델을 단계적으로 실험해보면서 정확도도 향상시키고 싶었다. GRU, LSTM을 직접 구현해보고 싶었는데 거기까진 못하고 w2v, CNN을 적용해봤다.  

![1라운드 기록]({{ site.url }}/assets/img/ai-hackathon-round1.png)
1라운드에서는 베이스코드에 레이어만 추가한 6-layer neural network를 사용했다. 여기에 모델의 initializer를 추가하고, dropout을 적용했다. 이정도만 해도 순위가 어느정도 나오길래 feature를 w2v을 이용하도록 변경하는 작업을 시작했다.  

한글 형태소 분석기는 [은전한잎](http://eunjeon.blogspot.kr/), [코모란](http://www.shineware.co.kr/products/komoran/), [트위터 토크나이저](https://github.com/open-korean-text/open-korean-text) 등이 있는데 트위터 토크나이저를 사용해서 토크나이즈한 뒤, [gensim](https://radimrehurek.com/gensim/)을 이용해 트레이닝했다. 그리고 [CNN for sentence classification](https://arxiv.org/abs/1408.5882)을 모델로 구현해봤다.  

여기서 테스트 결과가 잘 나왔다면 바로 RNN 모델들을 구현하는 스텝으로 갈 수 있었을텐데, 결과가 이전 6-레이어 모델보다 나빴다. 여러 이유가 있었겠지만 하이퍼 파라미터 튜닝을 여러번 해봐도 결과가 안나와서 기존 feature를 사용하도록 변경했다.  

![2라운드 기록]({{ site.url }}/assets/img/ai-hackathon-round2.png)
다행히 정확도가 5%이상 향상되어 80%를 넘게 되었다.  

커트라인이 40등까지라서 결선에 진출할 수 있었지만, 애초 목표는 달성해서 아쉬웠지만 여행을 다녀왔다.  

# nsml을 쓰면서
- 리더보드를 확인하면서 순위가 왔다갔다 하는걸 보는게 재밌었다. 괜히 더 열심히 하게 만들었다.  

- 로컬에서 돌려보는것과 nsml 플랫폼에 서브밋할 때 코드에서 차이가 없어서 편하게 잘 만들었다고 생각했다.  

- w2v을 이용해 트레이닝 셋의 단어들에 대해 임베딩을 생성하는 작업을 gensim을 이용해서 했는데, tensorflow를 이용해서 구현한게 아니다보니 시간이 좀 걸렸다. 여기서 테스트하는데 시간을 좀 소모했는데 nsml credit도 똑같이 깎여서 아까웠다. 모델을 트레이닝할 떄마다 클라우드에서 새로운 디렉토리를 만드는데, 이 부분은 클라이언트에게 가려져 있기 때문에, 내가 이전에 생성한 워드 임베딩을 재사용할 수 없었다. 그래서 테스트 할 때마다 임베딩을 다시 만들어서 사용하다보니 nsml 플랫폼을 사용하는 이점이 줄었다. 정확도라도 잘 나왔으면 좋았을텐데..  

# 끝으로
해커톤을 준비하면서 [네이버 무비 리뷰 데이터셋](https://github.com/e9t/nsmc)이 있는걸 찾았다. 지식인도 이런식으로 데이터셋이 있어서 사전에 내가 단어 임베딩을 준비할 수 있었으면 정확도 향상에 더 도움이 됐을것 같다.  

해커톤에 참가한 사람들이 [모델](https://github.com/naver/ai-hackathon-2018#%EB%AA%A8%EB%8D%B8-%EA%B3%B5%EC%9C%A0)을 공유해놨다(짝짝). 앞으로 참고해서 계획했던 모델들을 구현해야겠다.  
