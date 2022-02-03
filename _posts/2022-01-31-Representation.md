---
layout: single
title: "데이터 표현 (Representation)"
excerpt: "Feature Engineering, Qualities of Good Features, Cleaning Data"
toc: true
toc_sticky: true
categories:
  - machine-learning
---


---
## Feature Engineering

기존 프로그래밍에서는 코드를 짜는 것에 중점을 둔다. 그러나 머신러닝 프로젝트에서는 데이터의 표현에 중점을 둔다. 즉, 개발자가 모델을 연마하는 한 가지 방법은 특성을 추가하고 개선하는 것이다.


### Mapping Raw Data to Features

아래의 그림에서, 왼쪽은 입력 데이터를 그대로 표현한 것이다. 오른쪽은 같은 데이터를 실수(floating point)로 표현한 특성 벡터(feature vector)를 표현한 것이다. **Feature Engineering은 가공되지 않은 데이터를 특성 벡터로 변환하는 것을 의미한다.** 머신러닝 프로젝트의 대부분의 시간을 feature engineering에 할애하게 된다.

특성 값에 학습 가중치를 곱해야 하기 때문에 특성을 실수 벡터로 변환해야 한다.

<center><img src="{{site.baseurl}}/assets/images/representation1.png" /></center><br>


#### Mapping numeric values

정수 및 실수로 이루어진 데이터는 학습 가중치를 곱할 수 있기 때문에 특별한 변환이 필요하지 않다. 아래의 그림처럼 정수 6을 특성 값으로 만들어 주기 위해 실수 6.0으로 표현하는 작업은 간단하다.

<center><img src="{{site.baseurl}}/assets/images/representation2.png" /></center><br>


#### Mapping categorical values

범주형 특성에는 개별로 분리 가능한 값들을 가지고 있다. 예를 들어, 다음과 같이 `street_name`이라는 특성을 보자.

```
{'Charleston Road', 'North Shoreline Boulevard', 'Shorebird Way', 'Rengstorff Avenue'}
```

모델은 문자열 변수에 학습 가중치를 곱할 수 없기 때문에, 문자를 숫자로 변경하기 위해 feature engineering 과정을 거쳐야 한다.

어휘(vocabulary)로서의 특성 값을 조회하여 정수로 정의하여 이를 수행할 수 있다. 위의 `street_name`이라면, 전 세계의 모든 거리가 이 데이터에 표시되는 것은 아니므로 다른 거리들은 OOV(out-of-vocabulary)라고 불리는 포괄적인 '기타' 범주로 그룹화할 수 있다.

이 접근법을 사용하여 적용시키면 다음과 같다.
- Charleston Road = 0
- North Shoreline Boulevard = 1
- Shorebird Way = 2
- Rengstorff Avenue = 3
- 다른 모든 거리 = 4

그러나, 이렇게 변환된 값을 바로 모델 학습에 사용하면 몇 가지 문제가 될 수 있다.
- 모든 거리에 적용되는 단일 가중치를 학습할 것이다. 이 말이 무슨 뜻이냐면, `street_name`에 대해 학습한 가중치가 6인 경우, Charleston Road에 대해 0, North Shoreline Boulevard에 대해 1, Shorebird Way에 대해 2 등을 곱할 것이다. 모델은 각 거리의 이름이 아닌 `street_name`을 특성으로 사용한다는 것을 잊지 말아야 한다. 이렇게 학습 시킨다면, 거리의 이름에 기반하여 집값이 선형적으로 조정될 가능성은 거의 없다. 따라서, **다른 특성들에 의해 추정된 예측 값에 더해 각 거리에 따라 다른 가중치를 학습하는 유연성이 필요**하다.
- 또한 `street_name`의 값이 **여러 값을 가질 수 있는 경우**를 고려하지 않고 있다. 예를 들어, 어떤 집들은 두 거리의 코너에 위치하고 있다면 하나의 거리 값이 아닌 두 개의 거리 값이 입력되야 하지만 해당 정보를 입력할 수 없다.

이러한 제약들을 모두 제거하기 위해, 각 범주형 특성에 대해 **이진 백터(binary vector)**를 다음과 같이 생성할 수 있다.
- 데이터에 적용되는 값의 경우 해당 벡터 요소를 1로 설정한다.
- 다른 모든 요소를 0으로 설정한다.

즉, 데이터의 표현을 다음과 같이 변환하는 것이다.<br>
*(각 **column_name**과 **값**에 주목)*
```
{
  column_name : 'street_name'
  {
    house_0 : 0 (Charleston Road)
    house_1 : 1 (North Shoreline Boulevard)
    house_2 : 2 (Shorebird Way)
  }
}
==================================================
{
  column_name : 'Charleston Road'
  {
    house_0 : 1 (Charleston Road)
    house_1 : 0 (North Shoreline Boulevard)
    house_2 : 0 (Shorebird Way)
  }
}

{
  column_name : 'North Shoreline Boulevard'
  {
    house_0 : 0 (Charleston Road)
    house_1 : 1 (North Shoreline Boulevard)
    house_2 : 0 (Shorebird Way)
  }
}

{
  column_name : 'Shorebird Way'
  house_0 : 0 (Charleston Road)
  house_1 : 0 (North Shoreline Boulevard)
  house_2 : 1 (Shorebird Way)
}
```

이 벡터의 길이는 어휘의 요소 수와 같다. 이 표현을 단일 값이 1일 때 **one-hot encoding**이라고 하고, 여러 값이 1일 때 **multi-hot encoding**이라고 한다.

다음 그림은 'Shorebird Way' 거리의 one-hot encoding을 보여 준다. 'Shorebird Way'에 위치한 집의 값은 1이고 다른 거리에 위치한 집들은 모두 0의 값을 갖는다.

<center><img src="{{site.baseurl}}/assets/images/representation3.png" /></center><br>

이 접근법은 모든 특성 값에 대해 Boolean 변수를 효과적으로 생성한다. 위의 그림처럼 집이 특정 거리에 있으면, 그 거리에 해당하는 집만 1이고 나머지는 0이기 때문에 그 특정 거리에 대한 가중치만 사용할 수 있게 되는 것이다. 다른 거리들도 마찬가지로 하나의 특성으로 다루어지기 때문에 똑같다.

또한, 집이 두 거리의 코너에 위치하고 있다면 두 개의 거리에 대한 값이 1로 설정되기 때문에 모델 학습 시 각각의 가중치를 모두 사용할 수 있다.


#### Sparse Representation

그러나 만약, `street_name`에 포함된 서로 다른 거리의 이름이 1,000,000개가 있다고 가정하자. 집은 기껏 해봐야 거리 1개 또는 2개의 요소에만 중첩이 될텐데 1,000,000개가 넘는 값들을 이진 벡터로 생성하는 것은 메모리적으로도 시간적으로도 모두 매우 비효율적이다. 이런 상황에서의 일반적인 접근 방식은 0이 아닌 값만 저장되는 **희소 표현([sparse representation](https://developers.google.com/machine-learning/glossary#sparse_representation))**을 사용하는 것이다. 희소 표현 역시 각 특성 값에 대해 독립적인 모델 가중치가 학습된다.


---
## Qualitise of Good Features

위에서 가공되지 않은 데이터를 적절한 특성 벡터로 변환하는 방법을 알아보았다. 이제 **어떤 종류의 값을 변환해야 좋은 특성을 만들 수 있는지**를 알아야한다.

### Avoid rarely used discrete feature values

**좋은 특성 값은 데이터 세트에서 5번 이상은 나타나야 한다. 그래야 모델이 이 특성 값이 label과 어떻게 관련되는지 알 수 있다.** 즉, 동일한 별개의 값을 가진 많은 데이터를 사용하면 모델이 다른 설정들에서 특성을 살펴볼 수 있는 기회를 제공하고, 결과적으로 label에 대한 좋은 예측을 할 수 있다. 예를 들어, `house_type` 특성은 `victorian`이라는 값을 많이 포함하고 있을 수 있다.
```
house_type : victorian          o
```

반대로, 특성의 값이 한 번만 나타나거나 매우 드물게 나타나는 경우, 모델은 해당 특성을 기반으로 예측 값을 만들어 낼 수 없다. 예를 들어, `unique_house_id`는 좋지 않은 특성이다. 왜냐하면 **각 값은 오직 한 번만 사용되기 때문에 모델이 이 특성을 통해 배울 수 있는 것이 아무 것도 없기 때문이다.**
```
unique_house_id : 8SK982ZZ1242Z         x
```


### Prefer clear and obvious meanings

**각 특성은 프로젝트의 어느 누구에게도 명확하고 분명한 의미를 가져야 한다.** 예를 들어, 다음과 같은 좋은 특성은 이름이 명확하고 값은 특성의 이름과 관련하여 의미가 있다.
```
house_age_years : 27          o
```

반대로, 다음의 특성 값의 의미는 이것을 만든 엔지니어 외에는 어느 누구도 거의 해석할 수 없다.
```
house_age : 851472000         x
```

어떤 경우에는, 노이즈가 있는 데이터는 불분명한 값을 야기한다. 예를 들어, 다음 `user_age_year`는 적절한 값을 확인하지 않은 소스에서 가져왔다.
```
user_age_years : 277          x
```


### Don't mix "magic" values with actual data

좋은 실수 특성은 범위를 벗어난 독특한 불연속성이나 "마법" 값을 포함하지 않는다. 예를 들어, 특성이 0에서 1사이의 실수라고 가정하자. 그렇다면 다음과 같은 값이 좋다.
```
quality_rating : 0.82         o
quality_rating : 0.37         o
```

그러나, 사용자가 `quality_rating`을 입력하지 않았다면, 아마 데이터는 이것의 부재를 다음과 같은 "마법" 값으로 나타낼 수 있다.
```
quality_rating : -1         x
```

"마법" 값을 명시적으로 표시하기 위해서는, `quality_rating`이 입력되었는지 여부를 나타내는 Boolean 특성을 생성하는 것이 좋다. 이 특성의 이름은 `is_quality_rating_defined`과 같이 지정하는 것이 좋을 것이다.

원래의 특성에서, 마법 값을 다음과 같이 바꾼다.
- 유한한 값들의 집합(이산 변수들)을 사용하는 변수의 경우, 집합에 새 값을 추가하고 이를 사용하여 특성 값이 누락되었음을 나타낸다.
- 연속형 변수의 경우, 특성의 데이터의 평균 값을 사용하여 결측 값이 모델에 영향을 미치지 않도록 한다.


### Account for upstream instability

**특성의 정의는 시간의 지남에 따라 변경되어서는 안된다.** 예를 들어, 다음의 값은 유용하다. 왜냐하면 아마도 도시 이름은 바뀌지 않을 것이기 때문이다. (참고로 다음의 값은 문자열이기 때문에 one-hot 벡터로 변환해야 한다.)
```
city_id: "br/sao_paulo"         o
```

하지만, 다른 모델에 의해 추론된 값을 수집하는 것은 추가 비용이 든다. 아마도 '219'라는 값은 현재 상파울루를 나타내지만, 이 표현은 다른 모델의 향후 실행에서 쉽게 변경될 수 있다.
```
inferred_city_cluster: "219"          x
```


---
## Cleaning Data

사과 나무에서는 상태가 좋은 사과와 벌레 먹은 사과가 수확된다. 그러나 고급 식료품점에서 파는 사과들은 100% 완벽한 과일만 보인다. 과수원과 식료품점 사이에서 누군가가 상태가 좋지 않은 사과를 골라내거나 살릴 수 있는 사과는 왁스칠을 하는 등의 작업에 상당한 시간을 보낸다. **머신러닝 엔지니어는 좋지 않은 데이터를 버리고 복구 가능한 데이터를 정리하는 데에 엄청난 시간을 보내게 된다.** 몇 개의 "나쁜 사과"라도 큰 데이터 세트를 망칠 수 있기 때문이다.


### Scaling feature value

**Scaling은 실수의 특성 값을 그 특성 원래의 범위(예: 100 ~ 900)를 표준 범위(예: 0 ~ 1)로 변환하는 것을 의미한다.** 만약 데이터 세트가 하나의 특성으로만 구성된 경우에는 scaling을 해봐야 실질적인 이점이 없다. 그러나, 여러 특성들로 구성된 경우 scaling의 이점은 다음과 같다.
- **경사 하강법이 더 빠르게 수렴**되도록 돕는다.
- **모델의 한 숫자가 NaN이 되는 "NaN trap"을 방지**하는 데에 도움이 된다.(예를 들어, 학습 중 값이 실수의 정밀도 제한을 초과하는 경우에 Nan값이 되어버린다. 그리고 수학 연산을 통해 모델의 다른 모든 숫자들도 결국에는 NaN이 된다.)
- 모델이 **각 특성에 대한 적절한 가중치를 학습**하는 데에 도움이 된다. 특성을 scaling을 하지 않는다면, 모델은 학습 시에 더 넓은 범위의 특성에 훨씬 많은 주의를 기울인다.

모든 실수 특성에 정확히 동일한 범위를 지정할 필요는 없다. 특성 A가 -1 ~ +1로 변환되고, 특성 B가 -3 ~ +3으로 변환되었다고 해도 끔찍한 일은 일어나지 않는다. 그러나 특성 B가 5000 ~ 10000으로 변환되었다면 모델의 성능은 좋지 않을 것이다.

- 수의 데이터를 scaling하는 한 가지 확실한 방법은 **최소값을 -1, 최대값을 +1로 맞추어 그 사이에 있는 수들을 변환**하는 것이다.

- 또 하나의 인기 있는 scaling 전술은 **각 값의 Z-score를 계산**하는 것이다. Z-score는 각 값이 평균으로 부터 얼마나 떨어져 있는지를 측정하는 표준 편차와 관련된다.

<center><img src="{{site.baseurl}}/assets/images/representation4.png" /></center><br>

예시 :

- 평균(mean) : 100
- 표준 편차(standard deviation) : 20
- 측정 값(original value) : 130

따라서,

- scaling value(z-score) = (130 - 100) / 20 = 1.5

Z-score로 scaling을 하면 대부분의 변환된 값이 -3 ~ +3 사이에 있다. 하지만 일부 값은 해당 범위보다 약간 높거나 낮을 수 있다.


### Handling extreme outliers

다음 그래프는 *'California Housing data set'*의 `roomPerPerson`이라는 특성을 나타낸 것이다. 이 특성의 값은 해당 지역의 총 객실 수를 해당 지역의 총 인구로 나누어 계산되었다. 그래프는 캘리포니아의 대다수의 지역에 1인당 1 ~ 2개의 방이 있음을 보여준다. 그러나 x축을 보면,

<center><img src="{{site.baseurl}}/assets/images/representation5.png" /></center><br>

그래프의 꼬리가 매우 길다. 어떻게 해야 이러한 극단적인 이상 값의 영향을 최소화 할 수 있을까? 한 가지 방법은 **모든 값에 로그(log)**를 취하는 것이다.

<center><img src="{{site.baseurl}}/assets/images/representation6.png" /></center><br>

위의 그래프는 모든 값이 로그를 취한 결과이다. log scaling을 함으로써 약간 좋아졌지만 여전히 이상 값의 꼬리가 길다. 또 다른 접근 방식을 적용해보자. **임의의 값(예: 4.0)으로 최대값의 한도를 정하거나, 잘라내면** 어떻게 될까?

<center><img src="{{site.baseurl}}/assets/images/representation7.png" /></center><br>

위의 그림은 특성 값을 4.0에서 잘라낸 결과이다. 그러나 중요한 것은 4.0에서 잘라낸다고 해서 4.0보다 큰 모든 값을 무시한다는 의미는 아니다. 오히려 4.0보다 큰 모든 값들이 이제는 4.0이 됨을 의미한다. **즉, 임의의 값보다 큰 값들을 잘라내 버리는 것이 아니라 모두 임의의 값으로 만들어 버리는 것이다.** 그 결과 위의 그래프처럼 4.0의 수가 갑자기 솟아난 것을 볼 수 있다. 그럼에도 불구하고 원본 데이터보다 유용하다.


### Binning

다음 그래프는 캘리포니아의 다양한 위도에 있는 주택의 상대적인 보급룰을 보여준다. 참고로, 로스앤젤레스는 위도 34', 샌프란시스코는 대략 위도 38'이다.

<center><img src="{{site.baseurl}}/assets/images/representation8.png" /></center><br>

데이터 세트에서 위도는 실수 값이다. 그러나 모델에 위도를 특성으로 표현하는 것은 의미가 없다. 왜냐하면 위도와 주택 가격 사이에 선형 관계가 존재하지 않기 때문이다. 예를 들어, 위도 35'에 있는 집은 위도 34'에 있는 집보다 비싸지 않다. 그러나 개별 위도를 따로따로 본다면 아마 꽤 좋은 예측 변수일 것이다.

위도를 유용한 변수로 만들기 위해, 다음 그림과 같이 위도를 **재구성**한다.

<center><img src="{{site.baseurl}}/assets/images/representation9.png" /></center><br>

하나의 실수 특성 대신 11개의 구별가능한 boolean 특성(LatitudeBin1, ... LatitudeBin6, ...)으로 만들었다. 그러나 11개의 개별 특성은 다소 세련되지 않으므로, 11개의 요소를 가진 단일 벡터로 통합한다. 이렇게 하여 위도 37.4'를 다음과 같이 나타낼 수 있다.
```
[0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0]
```

binning 덕분에 이제 모델은 각 위도에 대해 완전히 다른 가중치를 학습할 수 있다.


### Scrubbing

지금까지는 학습 및 테스트에 사용되는 모든 데이터는 신뢰할 수 있다는 가정하에 공부했다. 하지만 실생활의 데이터 세트는 다음 보기의 하나 이상으로 인해 신뢰할 수 없는 경우가 많다.
- **생략된 값(Omitted values)** : 집의 건축 년도 값을 입력하는 것을 잊었다.
- **중복된 데이터(Duplicate data)** : 서버가 실수로 동일한 로그를 두 번 업로드했다.
- **잘못된 레이블(Bad label)** : 떡갈나무 그림을 단풍나무로 잘못 분류했다.
- **잘못된 특성 값(Bad feature value)** : 여분의 숫자를 입력했거나, 온도계가 햇볕에 버려져있었다.

일단 감지되면 일반적으로 데이터 세트에서 잘못된 데이터를 제거하여 "해결"한다. 생략된 값이나 중복된 데이터의 문제는 간단한 코드를 작성하여 감지할 수 있다. 그러나 잘못된 레이블이나 잘못된 특성 값을 감지하는 것은 훨씬 더 까다로울 수 있다.

나쁜 개별 데이터를 감지하는 것 뿐만 아니라, 집계를 통한 잘못된 데이터도 감지해야 한다. 히스토그램은 집계에서 데이터를 시각화하기 위한 훌륭한 방법이다. 또한 다음과 같은 통계 값을 얻는 것이 도움이 될 수 있다.
- **최대값 및 최소값(Maximum and minimun)**
- **평균값 및 중앙값(Mean and median)**
- **표준 편차(Standard deviation)**

불연속 특성에 대한 가장 일반적인 값의 리스트를 생성하는 것을 고려해야한다. 예를 들어, `country : UK`가 포함된 데이터 수를 예상한 수와 일치시켜라. `language : JP`가 데이터 세트에서 가장 일반적인 언어여야 하는가?


### Know your data

다음 규칙을 따르면 도움이 될 것이다.
- 데이터가 어떤 모습이어야 한다고 생각하는지 염두에 두어야한다.
- 데이터가 이러한 기대치를 충족하는지 확인한다. 또는 그렇지 않은 이유를 설명할 수 있어야 한다.
- 학습 데이터가 다른 소스(예: 대시보드)와 일치하는지 다시 확인하는 버릇을 들여야 한다.

미션 크리티컬 코드를 취급할 때와 같이 모든 주의를 기울여 데이터를 취급해야한다. **좋은 머신러닝은 좋은 데이터에 의존한다.**


<br>
<br>
<br>

---
※ 참고

[Google Developer - Feature Engineering](https://developers.google.com/machine-learning/crash-course/representation/feature-engineering)<br>
[Google Developer - Qualitise of Good Features](https://developers.google.com/machine-learning/crash-course/representation/qualities-of-good-features)<br>
[Google Developer - Cleaning Data](https://developers.google.com/machine-learning/crash-course/representation/cleaning-data)<br>