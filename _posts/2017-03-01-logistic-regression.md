---
layout: post
title: Logistic Regression의 Gradient descent 공식 유도
comments: true
excerpt: Logistic Regression의 Gradient descent 공식 유도
---
2년 전에 Coursera에 개설된 머신러닝 코스를 들었는데, 업무에 인공지능을 적용해보려고 머신러닝을 복습하고 있다. 요즘 '딥러닝 첫걸음'이라는 책을 읽고 있는데, 식 표기나 공식 유도 방식이 달라서 헷갈려서 기억을 더듬어서 공식을 유도해봤다. 거의 다 잊어버려서 생각보다 시간이 걸렸기 때문에 기록 차원에서 남겨두려고 한다.

Binary Classification 문제는 Linear Regression(선형회귀)와 다르게 결과값이 0 또는 1로 분류되어야 한다. Sigmoid 함수의 결과는 범위가 [0, 1]이라서 이 문제에서 유용하게 사용할 수 있다.

$$ g(z) = \frac{1}{1+e^{-z}} $$

$$ g'(z) = g(z)(1-g(z)) $$

문제를 간략하게 만들기 위해 Bias는 없다고 가정하고, *n*차원의 feature 벡터 $$\mathbf{x}^{(i)} \text{ } (i=1...m)$$ *m*개를 이용해 같은 차원의 파라미터 벡터 $$\mathbf{w}$$를 학습시키고자 한다. 우리의 학습모델 $$h$$ 와 비용함수 $$J(\mathbf{w})$$는 다음과 같다:

$$ h(\mathbf{x}) = g(\mathbf{w^Tx}) $$  

$$ J(\mathbf{w}) = \frac{1}{m}\sum_{i=1}^{m}cost(\mathbf{x^{(i)}, y^{(i)}}) $$  

$$ cost(\mathbf{x^{(i)}, y^{(i)}}) =
 \begin{cases} 
 -log(h(\mathbf{x^{(i)}})) & \text{if y=1} \\
 -log(h(1-\mathbf{x^{(i)}})) & \text{if y=0} \\
 \end{cases} $$

 문제가 binary classification이므로 y의 값은 무조건 0 또는 1이다. 따라서 cost function을 간략하게 하나의 식으로 나타낼 수 있는데, 다음과 같다.

 $$ cost(\mathbf{x^{(i)}, y^{(i)}}) = -\mathbf{y^{(i)}}log(h(\mathbf{x^{(i)}})) -(1-\mathbf{y^{(i)}})log(h(1-\mathbf{x^{(i)}})) $$

$$\mathbf{w}$$를 gradient descent 최적화 방식을 이용해 주려면, $$w$$에 따른 비용함수의 변화량, 즉 미분값을 이용해서 업데이트 해줘야 한다.

$$\mathbf{w} := \mathbf{w} - \alpha\frac{\partial}{\partial \mathbf{w}}J(\mathbf{w}) \text{ where } \alpha \text{ is learning rate}$$

결론부터 말하면, $$ \frac{\partial}{\partial \mathbf{w}}J(\mathbf{w}) $$는 선형회귀에서 사용하는 업데이트 식과 모양이 같이 나온다. $$\mathbf{w}$$의 j번째 요소에 대해서 변화량을 살펴보자.

$$ \frac{\partial}{\partial \mathbf{w}_j}J(\mathbf{w}) = \frac{\partial}{\partial \mathbf{w}_j} - \frac{1}{m}\sum_{i=1}^{m} \bigg( \mathbf{y^{(i)}}log(h(\mathbf{x^{(i)}})) + (1-\mathbf{y^{(i)}})log(1-h(\mathbf{x^{(i)}})) \bigg) \\
= -\frac{1}{m}\sum_{i=1}^{m} \bigg( \mathbf{y^{(i)}}\frac{\partial}{\partial \mathbf{w}_j}log(h(\mathbf{x^{(i)}})) + (1-\mathbf{y^{(i)}})\frac{\partial}{\partial \mathbf{w}_j}log(1-h(\mathbf{x^{(i)}}) \bigg)  \\
= -\frac{1}{m}\sum_{i=1}^{m} \bigg( \mathbf{y^{(i)}}\frac{h'(\mathbf{x^{(i)}})}{h(\mathbf{x^{(i)}})} \frac{\partial}{\partial \mathbf{w}_j} \mathbf{w}^T\mathbf{x}^{(i)} + (1-\mathbf{y^{(i)}})\frac{-h'(\mathbf{x^{(i)}})}{1-h(\mathbf{x^{(i)}})} \frac{\partial}{\partial \mathbf{w}_j} \mathbf{w}^T\mathbf{x}^{(i)} \bigg) \\
= -\frac{1}{m}\sum_{i=1}^{m} \bigg( \mathbf{y^{(i)}} \frac{h(\mathbf{x^{(i)}})(1-h(\mathbf{x^{(i)}}))}{h(\mathbf{x^{(i)}})} \mathbf{x}^{(i)}_j + (1-\mathbf{y^{(i)}}) \frac{-h(\mathbf{x^{(i)}})(1-h(\mathbf{x^{(i)}}))}{1-h(\mathbf{x^{(i)}})} \mathbf{x}^{(i)}_j \bigg) \\
= \frac{1}{m}\sum_{i=1}^{m} ( h(\mathbf{x}^{(i)}) - \mathbf{y^{(i)}} ) \mathbf{x}^{(i)}_j
$$

이 식을 벡터 $$\mathbf{w}$$에 대해서 다시 써주면 다음과 같다:

$$\mathbf{w} := \mathbf{w} - \alpha\frac{1}{m}\sum_{i=1}^{m} ( h(\mathbf{x}^{(i)}) - \mathbf{y^{(i)}} ) \mathbf{x}^{(i)}$$

이상으로 Logistic regression에 쓰이는 업데이트 함수를 유도해봤다. 식의 모양은 선형 회귀에서 쓰이는 것과 같고, 모델(hypothesis)가 다르다는 것을 이해하면 된다.

Reference  
[Machine Learning - complete course notes](http://www.holehouse.org/mlclass/)
