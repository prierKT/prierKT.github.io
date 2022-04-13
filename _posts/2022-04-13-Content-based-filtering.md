---
layout: single
title: "추천 시스템: 컨텐츠 기반 필터링 (Content-based filtering)"
excerpt: "Basics, Advantages & Disadvantages"
toc: false
toc_sticky: false
categories:
  - machine-learning
---

**컨텐츠 기반 필터링(content-based filtering)은 `item`의 특성을 사용하여 사용자의 이전 활동 또는 명시적 피드백을 기반으로 사용자가 좋아하는 것과 유사한 다른 `items`를 추천한다.**

컨텐츠 기반 필터링을 설명하기 위해, Google Play Store의 몇 가지 기능을 직접 엔지니어링 해보자. 아래의 그림은 각 행이 앱을 나타내고, 각 열이 특성을 나타내는 특성 행렬이다. 특성에는 카테고리(교육, 캐주얼, 건강), 앱의 게시자 등이 포함될 수 있다. 단순화하기 위해, 이 특성 행렬을 이진법으로 가정하겠다. 값이 0이면 '해당 특성 없음', 0이 아니면 '해당 특성 있음'을 의미한다.

또한 동일한 특성 공간에서 사용자를 나타낸다. 사용자 관련 특성 중 일부는 사용자에 의해 명시적으로 제공될 수 있다. 예를 들어, 사용자가 'Entertainment' 앱을 선택한다고 하자. 다른 특성은 이전에 설치한 앱에 기반함을 암시할 수 있다. 예를 들어, 사용자가 'Science R Us'에서 게시한 다른 앱을 추가로 설치했다.

모델은 이 사용자와 관련된 `items`를 추천해야 한다. 그러기 위해서, 먼저 유사도 측정법(내적, 코사인 등)을 선택해야 한다. 그런 다음 이 유사도 측정법에 따라 각 후보에 점수를 매기도록 시스템을 구축해야 한다. **이 모델은 다른 사용자에 대한 정보를 사용하지 않았기 때문에, 추천은 이 사용자에게만 해당된다.**

<center><img src="{{site.baseurl}}/assets/images/content_based1.png" /></center><br>


### 내적으로 유사도 측정 (Using Dot Product as a Similarity Measure)

여기서는 ***x***를 포함하는 사용자와 ***y***를 포함하는 앱이 모두 이진 벡터라고 가정한다. 내적을 사용할 경우의 유사도는 <img src="{{site.baseurl}}/assets/images/content_based2.png" />이므로, ***x***와 ***y***에 모두 나타나는 특성은 합에 1을 기여한다. 즉, <img src="{{site.baseurl}}/assets/images/content_based3.png" />는 두 벡터에서 동시에 활성화되는 특성의 수이다. **높은 내적은 더 많은 공통 특성을 나타내므로 더 높은 유사도를 나타낸다.**


---
## 장점 & 단점 (Advantages & Disadvantages)

> **장점**
- 추천은 이 사용자에게만 적용되므로 모델에는 다른 사용자에 대한 데이터가 필요하지 않다. 이는 많은 사용자로 쉽게 확장할 수 있다.
- 이 모델은 사용자의 특정 관심사를 반영할 수 있으며, 다른 사용자가 관심을 갖지 않는 틈새 `items`를 추천할 수 있다.

> **단점**
- `items`의 특성 표현은 어느 정도 수공으로 엔지니어링되기 때문에, 이 기술은 많은 도메인 지식이 필요하다. 따라서 모델은 수공으로 엔지니어링된 특성만큼만 우수할 수 있다.
- 모델은 사용자의 기존 관심사를 기반으로만 추천할 수 있다. 즉, 모델은 사용자의 기존 관심사를 확장하는 능력이 제한적이다.

<br>
<br>
<br>

---
※ 참고

[Google Developer - Content-based Filtering](https://developers.google.com/machine-learning/recommendation/content-based/basics)<br>
[Google Developer - Content-based Filtering Advantages & Disadvantages](https://developers.google.com/machine-learning/recommendation/content-based/summary)<br>