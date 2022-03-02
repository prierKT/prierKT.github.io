---
layout: single
title: "신경망 학습 (Training Neural Nets)"
excerpt: "Best Practices"
toc: true
toc_sticky: true
categories:
  - machine-learning
---

역전파(Backpropagation)는 신경망에 대한 가장 일반적인 학습 알고리즘이다. 역전파는 여러 층의 신경망에 대해 경사 하강법을 실현할 수 있다. 역전파에 대한 시각적인 설명은 [Backpropagation algorithm visual explanation](https://developers-dot-devsite-v2-prod.appspot.com/machine-learning/crash-course/backprop-scroll)을 참고하자.

**역전파는 동적 프로그래밍을 통해 기하급수적으로 많은 경로를 계산하는 것을 피할 수 있다. 동적 프로그래밍이란, 순방향 및 역방향 흐름에서 중간 결과를 기록하는 것을 의미한다.**


---
## 모볌 사례 (Best Practices)

역전파의 실패 사례와 신경망을 정규화하는 가장 일반적인 방법에 대해 알아보자.


### 실패 사례 (Failure Cases)

역전파가 잘못되는 몇 가지 공통적인 방법이 있다.

#### 경사 소실 (Vanishing Gradients)

하위 레이어의 기울기는 매우 작아질 수 있다. 심층 네트워크에서, 이러한 기울기를 계산하는 것은 많은 작은 항의 곱을 취하는 것을 포함할 수 있다.

하위 레이어의 **기울기가 0을 향해 사라질 때, 이 레이어는 학습이 매우 느리거나, 전혀 학습되지 않는다.**

활성화 함수인 'ReLU'는 경사 소실을 방지하는 데 도움이 될 수 있다.


#### 기울기 폭주 (Exploding Gradients)

네트워크의 가중치가 매우 크면, 하위 레이어에 대한 기울기는 많은 큰 항의 곱을 포함한다. 이 경우 기울기 폭주가 있을 수 있다. **수렴하기에는 너무 큰 값을 가지는 기울기이다.**

배치 정규화(Batch normalization)는 learning rate를 낮출 수 있는 것처럼 기울기 폭주를 방지하는데 도울이 될 수 있다.


#### Dead ReLU

**ReLU에 대한 가중치 합이 0 아래로 떨어지면, ReLU가 멈출 수 있다.** 활성화 값을 0으로 출력하여, 네트워크 출력에 아무 기여도 하지 않으며, 역전파 동안 기울기가 더 이상 이를 통해 흐를 수 없다. 기울기 소스가 차단되면, ReLU에 대한 입력이 가중치 합을 0 위로 되돌릴 만큼 충분히 변하지 않을 수 있다.

learning rate를 낮추는 것은 ReLU가 죽는 것을 막을 수 있다.


### Dropout Regularization

Dropout이라고 하는 또 다른 정규화 형식은 신경망에 유용하다. **하나의 기울기 단계에 대해 네트워크에서 단위 활성화를 무작위로 삭제하여 작동한다.** 더 많이 삭제될수록 정규화가 더 강해진다.

- 0.0 = Dropout 정규화 없음
- 1.0 = 모든 것을 삭제. 모델은 아무것도 학습하지 않는다.
- **0.0 과 0.1 사이의 값 = 주어진 값의 비율로 정규화.**



<br>
<br>
<br>

---
※ 참고

[Google Developer - Best Practices](https://developers.google.com/machine-learning/crash-course/training-neural-networks/best-practices)<br>