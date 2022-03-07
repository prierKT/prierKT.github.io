---
layout: single
title: "다중 클래스 신경망 (Multi-Class Neural Networks)"
excerpt: "One vs. All, Softmax"
toc: true
toc_sticky: true
categories:
  - machine-learning
---

이전에는 다음과 같은 두 가지의 클래스 중 하나를 예측하는 이진 분류 모델을 공부했다.
- 주어진 이메일은 스팸인가 스팸이 아닌가.
- 주어진 종양은 악성인가 양성인가.

이제 **여러 클래스를 갖는 다중 클래스 분류**를 알아보자. 예를 들어,
- 이 강아지는 진돗개, 풍산개, 삽살개, ... 중 어느 종인가?
- 이 꽃은 장미, 튤립, 코스모스, ... 중 어느 종인가?
- 이 비행기는 아시아나, 대한항공, 진에어, ... 중 어디 소속인가?
- 이것은 사과, 사탕, 계란, ... 중 어떤 것인가?

일부 실제 다중 클래스 문제는, 거의 모든 사물의 이미지를 식별할 수 있도록, 수백만 개의 클래스 중에서 예측을 해야햐는 문제도 있다.


---
## 일대 다(One vs. All)

**일대 다(One vs. All)문제는 이진 분류를 활용하는 방법을 제공한다. N개의 가능한 솔루션이 있는 분류 문제가 주어지면, 일대 다 솔루션은 N개의 각각의 출력이 가능한 개별 이진 분류기로 구성된다.** 학습하는 동안, 모델은 일련의 이진 분류기들을 통해 실행되며, 각각의 개별 분류 질문에 답하도록 학습한다. 예를 들어, 개의 사진이 주어지면, 다음과 같이 5개의 서로 다른 인식기가 학습될 수 있다. 4개는 부정적인 답(개가 아니다.)을 내고 1개는 긍정적인 답(개가 맞다.)을 낸다.
- 이 사진은 사과인가? 아니다.
- 이 사진은 곰인가? 아니다.
- 이 사진은 사탕인가? 아니다.
- 이 사진은 개인가? 그렇다.
- 이 사진은 달걀인가? 아니다.

**이 접근 방식은 전체 클래스의 수가 적을 때는 상당히 합리적이지만, 클래스 수가 증가할수록 점점 비효율적이 된다.**

각 출력 노드가 다른 클래스를 나타내는 심층 신경망을 사용하여 훨씬 더 효율적인 일대 다 모델을 만들 수 있다. 다음 그림과 같다.

<center><img src="{{site.baseurl}}/assets/images/multiclass1.png" /></center><br>


---
## Softmax

[로지스틱 회귀](https://prierkt.github.io/machine-learning/LogisticRegression/)는 0 과 1 사이의 소수를 생성한다고 했다. 예를 들어, 로지스틱 회귀를 활용한 이메일 분류 모델이 0.8의 값을 출력했다는 것은 스팸일 확률이 80%, 스팸이 아닐 확률이 20%임을 의미한다. 분명히, 이메일이 스팸이거나 스팸이 아닐 확률의 합은 1 이다.

**Softmax**는 이 아이디어를 다중 클래스 세계로 확장한다. 즉, **Softmax는 다중 클래스 문제에서 각 클래스에 소수를 할당한다. 이러한 소수 확률들을 더하면 1이 된다.** 이 추가적인 제약은 그렇지 않은 경우보다 학습 시 더 빠르게 수렴하도록 돕는다.

예를 들어, 아래의 표를 분석해보자. Softmax는 특정 클래스에 속하는 이미지에 대해 다음과 같은 가능성을 생성할 수 있다.

<center><img src="{{site.baseurl}}/assets/images/multiclass2.png" /></center><br>

**Softmax는 출력층 바로 앞의 신경망 계층을 통해 구현된다. Softmax 계층에는 출력층과 동일한 수의 노드가 있어야 한다.**

<center><img src="{{site.baseurl}}/assets/images/multiclass3.png" /></center><br>

Softmax의 방정식은 다음과 같다.
<center><img src="{{site.baseurl}}/assets/images/multiclass4.png" /></center><br>

위 공식은 기본적으로 로지스틱 회귀 공식을 다중 클래스로 확장한 것이라는 것에 주목하라.


### Softmax Options

다음의 Softmax의 변형들이 있다.
- **Full Softmax**는 위에서 언급한 Softmax이다. 즉, **가능한 모든 클래스에 대한 확률을 계산한다.**
- **Candidate sampling**은 Softmax가 **positive label에 대해서는 모든 확률을 계산하지만, negative label은 임의의 샘플에 대해서만 확률을 계산한다.** 예를 들어, 입력 이미지가 비글인지, 말티즈인지 확인하는 데에 관심이 있는 경우, 강아지가 아닌 모든 클래스에 대한 확률은 제공할 필요가 없는 것이다.

Full Softmax는 클래스 수가 적으면 상당히 저렴하지만 클래스 수가 많으면 굉장히 비싸다. Candidate sampling은 클래스 수가 많은 문제에서 효율성을 향상 시킬 수 있다.


### One Label vs. Many Labels

Softmax는 각 데이터가 정확히 한 클래스에 속할 것이라고 가정한다. 그러나 **일부 데이터는 동시에 여러 클래스에 속할 수 있다.** 이럴 때는,
- Softmax를 사용하면 안될지도 모른다.
- **다중 로지스틱 회귀에 의지해야 한다.**

예를 들어, 입력 이미지가 과일 이미지(예: 한 과일의 조각)라고 가정하자. Softmax는 이 입력 이미지가 배, 오렌지, 사과 등일 가능성을 결정할 수 있다. 입력 이미지가 모든 종류의 과일을 포함하는 이미지(예: 과일 바구니)인 경우라면, 다중 로지스틱 회귀를 사용해야 한다.

<br>
<br>
<br>

---
※ 참고

[Google Developer - One vs. All](https://developers.google.com/machine-learning/crash-course/multi-class-neural-networks/one-vs-all)<br>
[Google Developer - Softmax](https://developers.google.com/machine-learning/crash-course/multi-class-neural-networks/softmax)<br>