---
layout: single
title: "추천 시스템이란? (What is Recommendation System?)"
excerpt: "What and Why?, Terminology, Overview"
toc: false
toc_sticky: false
categories:
  - machine-learning
---


## 추천: 무엇을? 그리고 왜? (Recommendation: What and Why?)


### 추천이란 무엇인가? (What are Recommendations?)

YouTube는 사용자가 다음에 보고 싶어 하는 영상을 어떻게 알 수 있는가? Google Play Store는 어떻게 나만을 위한 앱을 선택하는가? 마법인가? 아니다. 두 경우 모두, **ML 기반 추천 모델이 특정 영상 및 앱이 사용자가 좋아하는 아이템과 얼마나 유사한지 판단한 다음 추천을 제공한다.** 일반적으로 두 종류의 추천이 사용된다.
- home page recommendations
- related item recommendations


#### Homepage Recommendation

**Homepage Recommendation은 알려진 관심사를 기반으로 사용자에게 개인화된다. 모든 사용자는 다른 추천을 받는다.**

Google Play 앱의 홈페이지로 이동하면 다음과 같은 추천을 볼 수 있을 것이다.

<center><img src="{{site.baseurl}}/assets/images/recommendation1.png" /></center><br>


#### Related Item Recommendation

이름에서 알 수 있듯이, **Related Item은 특정 아이템과 유사한 아이템을 찾아서 추천한다.** Google Paly를 예로 들면, 수학 앱에 대한 페이지를 보고 있는 사용자는 다른 수학 앱 또는 과학 앱과 같은 관련 패널도 볼 수 있다.


### 추천을 왜 하는가? (Why Recommendation?)

**추천 시스템은 사용자가 대규모 코퍼스에서 매력적인 콘텐츠를 찾는 데 도움이 된다.** 예를 들어, Google Play Store는 수백만 개의 앱을 제공하고, YouTube는 수십억 개의 영상을 제공한다. 거기다 매일 더 많은 앱과 영상이 추가된다. 사용자는 새롭고 매력적인 새 콘텐츠를 어떻게 찾는가? 물론 검색을 사용하여 콘텐츠에 접근할 수 있다. 그러나, 추천 엔진은 사용자가 스스로 생각하지 못한 아이템을 보여줄 수 있다.


*YouTube 시청 시간의 60%, Google Paly 앱 설치의 40%는 추천을 통해 이루어진다.*


---
## 용어 (Terminology)

자세히 알아보기 전에, 알아야 할 몇 가지 용어들이 있다.


### Item (also known as document)

**시스템이 추천하는 독립체.** Google Play Store의 경우 `item`은 설치할 앱이다. YouTube의 경우 `item`은 영상이다.


### Query (also known as context)

**시스템이 추천을 만드는데 사용하는 정보.** `query`는 다음을 조합할 수 있다.
- 사용자 정보
  - 사용자의 아이디
  - 사용자가 이전에 상호 작용한 `items`

- 추가 사항
  - 날짜, 시간
  - 사용자의 장치


### 임베딩 (Embedding)

**별개의 집합(query 집합 또는 추천할 items 집합)에서 임베딩 공간이라고 불리는 벡터 공간으로의 매핑이다.** 많은 추천 시스템들이 query 및 items의 적절한 임베딩 표현을 학습하는 데 의존한다.


---
## 추천 시스템의 개요 (Recommendation Systems Overview)

추천 시스템을 위한 하나의 공통 아키텍쳐는 다음 구성 요소로 구성된다.
- **후보 생성 (candidate generation)**
- **채점 (scoring)**
- **순위 재지정 (re-ranking)**

<center><img src="{{site.baseurl}}/assets/images/recommendation2.png" /></center><br>


### 후보 생성 (Candidate Generation)

이 첫 번째 단계에서, **시스템은 잠재적으로 거대한 코퍼스에서 시작하여 줄이고 줄여서 후보의 하위 집합을 생성한다.** 예를 들어, YouTube의 `candidate generator`는 수십억 개의 영상을 수천 또는 수백 개의 영상으로 줄인다. 따라서 모델은 방대한 양의 코퍼스를 감안할 때 `query`를 빠르게 평가해야 한다. 모델은 각각 다른 후보 집합을 생성하는 여러 개의 `candidate generator`를 제공할 수 있다.


### 채점 (Scoring)

다음으로, **다른 모델은 사용자에게 표시할 `items`의 집합을 선택하기 위해 후보들의 점수를 매기고 순위를 매긴다.** 이 모델은 상대적으로 `items`의 작은 하위 집합을 평가하기 때문에, 시스템은 추가 `query`에 의존하여 보다 정확한 모델을 사용할 수 있다.


### 순위 재지정 (Re-ranking)

마지막으로, **시스템은 최종 순위에 대한 추가 제약을 고려해야만 한다.** 예를 들어, 시스템은 사용자가 명시적으로 싫어하는 `items`을 제거하거나 최신 콘텐츠의 점수를 높인다. 따라서 순위 재지정은 다양성, 새로움 및 공정성을 보장하는 데 도울이 될 수 있다.

*YouTube에서 사용하는 기술에 대해 더 궁금하면 [Covington et al., Deep Neural Networks for YouTube Recommendations.](https://research.google/pubs/pub45530/)를 참고.*

<br>
<br>
<br>

---
※ 참고

[Google Developer - Recommendations: What and Why?](https://developers.google.com/machine-learning/recommendation/overview)<br>
[Google Developer - Terminology](https://developers.google.com/machine-learning/recommendation/overview/terminology)<br>
[Google Developer - Recommendation Systems Overview](https://developers.google.com/machine-learning/recommendation/overview/types)<br>