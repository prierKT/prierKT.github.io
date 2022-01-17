---
layout: single
title: "머신러닝 시작하기"
excerpt: "Linear Regression, Training, Loss"
toc: true
toc_sticky: true
categories:
  - machine-learning
---


---
## 선형 회귀(Linear Regression)

귀뚜라미는 추운 날보다 더운 날에 더 자주 운다는 것으로 알려져 있다. 수십 년 동안, 전문 및 아마추어 과학자들은 분당 울음과 온도에 대한 데이터를 수집해왔다.
생일 선물로 이모가 귀뚜라미 데이터베이스를 주면서, 이 관계를 예측하는 모델을 학습시켜 달라고 요청했다. 이 데이터를 사용하여 관계를 탐구해보자.

먼저, 데이터를 시각화 하여 조사해보면 :

<center><h5>분당 울음 vs. 온도</h5></center>
<center><img src="{{site.baseurl}}/assets/images/lr1.png" /></center><br>


예상대로, 울음의 수에 따라 온도가 올라가는 것을 볼 수 있다. 그렇다면, 울음과 온도 사이의 관계는 선형인가? 대답은 그렇다. 이 관계에 근사치를 갖는 직선을 그려보면 :

<center><h5>선형 관계</h5></center>
<center><img src="{{site.baseurl}}/assets/images/lr2.png" /></center><br>

사실, 그린 직선은 모든 점들을 지나지는 않는다. 하지만 이 직선은 울음과 온도의 관계를 분명하게 보여준다. 선에 대한 방정식을 사용하여 다음과 같이 이 관계를 표현할 수 있다.

<center><img src="{{site.baseurl}}/assets/images/lr3.png" /></center><br>

- **y**는 온도이다. (예측되어야 하는 값)
- **m**은 직선의 기울기이다.
- **x**는 분당 울음 수이다. (입력 특성 값)
- **b**는 y절편이다.

머신러닝에서는 이 방정식을 약간 다르게 표현한다.

<center><img src="{{site.baseurl}}/assets/images/lr4.png" /></center><br>

- **y'**는 예측되는 레이블 (출력값)
- **b**은 편향(y절편)이며, 때로는 **w0**이라고도 한다.
- **w1**는 특성 1의 가중치이다. 가중치는 선의 방정식의 기울기(m)와 같은 개념이다.
- **x1**는 특성이다. (입력값)

새로운 분당 울음 수 값(x1)에 대해 온도 (y')를 예측하기 위해서는, 모델에 x1값을 대입하면 된다.

이 모델은 하나의 특성만을 사용하지만, 보다 정교한 모델은 각각 별도의 가중치(w1, w2, ...)를 갖는 여러 특성(x1, x2, ...)을 사용하여 만들 수 있다. 예를 들어, 세 개의 특성으로 만들어진 모델은 다음과 같이 표현할 수 있다.

<center><img src="{{site.baseurl}}/assets/images/lr5.png" /></center><br>

---


## Training(훈련) and Loss(손실)

모델을 **Training(훈련)**한다는 것은 단순히 레이블이 지정된 데이터로부터 모든 가중치와 편향에 대한 좋은 값을 학습하는 것을 의미한다. Supervised learning(지도 학습)에서, 머신 러닝 알고리즘은 많은 데이터를 검토하고 손실을 최소화하는 모델을 찾기 위해 시도하여 모델을 만든다. 이 과정을 **empirical risk minimization(경험적 위험 최소화)**라고 부른다.

**Loss(손실)**은 잘못된 예측에 대한 패널티이다. 즉, loss는 단일 데이터에서 모델의 예측이 얼마나 잘못되었는지를 나타내는 숫자이다. 만약 모델의 예측이 완벽하다면, loss는 0이다. 그렇지 않으면, loss는 커진다. 모델 훈련의 목표는 모든 데이터에서 평균적으로 손실이 낮은 가중치와 편향을 찾는 것이다. 예를 들어, 아래의 그림에서 왼쪽은 높은 loss를 가진 모델이고, 오른쪽은 낮은 loss를 가진 모델이다. 

- 빨간 화살들은 loss를 표현한 것이다.
- 파란 선은 예측을 표현한 것이다.

<center><h5>high loss(왼쪽) vs. low loss(오른쪽)</h5></center>
<center><img src="{{site.baseurl}}/assets/images/loss1.png" /></center><br>

왼쪽 그래프의 화살들이 오른쪽 그래프의 화살들보다 긴 것에 주목해야 한다. 확실히 오른쪽 그래프의 선이 왼쪽 그래프의 선보다 훨씬 더 좋은 예측 모델이다.

그렇다면, 각각의 loss를 의미 있는 방식으로 집계하는 손실 함수는 어떻게 만들 수 있을까?


### Squared loss(제곱 손실)

이 포스트에서 다루는 선형 회귀 모델은 **squared loss(제곱 손실)**이라고 불리는 손실 함수를 사용한다. 또한 **L2 loss**라고도 불린다. 단일 데이터에 대한 제곱 손실은 :

```
= 레이블과 예측값의 차의 제곱
= (실제값 - x에 대한 예측값)2
= (y - y')2
```
<br>
**Mean Squared Error(MSE:평균 제곱 오차)**는 전체 데이터 세트에 대한 각 데이터의 제곱 손실의 평균이다. MSE의 계산은, 각 데이터의 제곱 손실을 모두 더하고 데이터의 수로 나눈다.

<center><img src="{{site.baseurl}}/assets/images/mse1.png" /></center><br>

- **(x, y)**는 데이터이다.
  - **x**는 모델이 예측하기 위해 사용되는 특성들(예: 귀뚜라미의 울음소리, 나이, 성별 등)이다.
  - **y**는 데이터의 레이블(예: 온도)이다.
- **prediction(x)**는 특성들(x)와 결합된 가중치와 편향의 함수이다. (모델이 예측한 값)
- **D**는 많은 데이터들이 포함되어 있는 데이터 세트이다. (x, y)의 모임.
- **N**은 D의 수이다. 전체 데이터 수.

비록 MSE가 머신 러닝에서 자주 사용되지만, 유일한 실용적 함수도 아니고 모든 상황에 대한 최고의 함수도 아니다. 따라서, 상황에 따라 적절한 손실 함수를 사용하는 것이 중요하다.

<br>
<br>
<br>

---
※ 참고

[Google Developer - Linear Regression](https://developers.google.com/machine-learning/crash-course/descending-into-ml/linear-regression)<br>
[Google Developer - Training and Loss](https://developers.google.com/machine-learning/crash-course/descending-into-ml/training-and-loss)