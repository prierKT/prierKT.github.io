---
layout: single
title: "추천 시스템(Recommender System)의 이해"
excerpt: "연관 분석, Apriori, FP-Growth"
toc: true
toc_sticky: true
categories:
  - ai
tags:
  - recommender system
---

학원 수료 프로젝트로 음악 추천 프로그램을 만들었는데 이때는 추천 시스템의 이해도가 없이 막연하게 만들었다. 코딩 자체를 배운지 6개월 밖에 되지 않은 시점이라서 코드가 굉장히 지저분하고 모델의 구성도 형편없었다. 그러다가 머신러닝 엔지니어로서 취업을 하기 위해 포트폴리오를 만들던 중 이 형편없는 프로젝트를 살려보자고 생각하게 되었고, 추천 시스템에 대해서 자세하게 공부해 보기로 했다.


## 추천 시스템 개요.

추천 시스템은 **사용자(user)에게 상품(item)을 제안**하는 기술이다. 이러한 제안은 사용자가 어떤 상품을 구매할 것인지, 어떤 음악을 듣을 것인지, 어떤 영화를 볼 것인지 등과 같은 다양한 의사결정에 기여한다.

따라서 **어떤 사용자에게 어떤 상품을 어떻게 추천**할 것인지 이해할 필요가 있다.


### 파레토와 롱테일의 법칙.

- 파레토의 법칙 : 상위 20%가 80%의 가치를 창출한다.

파레토의 법칙은 기업 매출의 80%가 20%의 VIP나 상품에서 발생하기 때문에 핵심 고객이나 핵심 상품에 주력하는 것이 좋다는 관점에서 나온 이론이다.

- 롱테일의 법칙 : 하위 80%가 상위 20%가치보다 크다.

반면 인터넷의 발전으로 인해 어느 분야든 일반 사용자들의 접근이 쉽고 활발해지면서 아마존이나 넷플릭스 같은 기업들은 롱테일의 법칙에 집중하여 추천 시스템을 개발하였다. 아마존의 경우 서점에서 팔리지 않는 책들을 온라인에서 많이 판매했고, 매출의 50% 이상이 추천 시스템을 통한 온라인에서 이루어졌다고 한다.


## 추천 시스템의 역사.

<center><img src="{{site.baseurl}}/assets/images/RS_history.jpg" /></center><br>

- Apriori 알고리즘
  - 연관 상품 추천

추천 시스템의 출발이다. 어떤 상품을 구매할 때, 같이 구매하는 상품을 찾아서 추천해 주는 알고리즘이다. 이로 인해 추천 시스템에 대한 관심이 늘어나 오프라인 또는 온라인 마케팅에 어떻게 적용할 것인가 고민하기 시작했다.

- 협업 필터링
  - SVD

2006년부터 2009년까지 영화 추천 모델을 개발하는 넷플릭스 추천 대회에서 우승한 솔루션이 SVD를 통한 협업 필터링 기반 모델이었다. 이 시점부터 협업 필터링을 기반으로 하는 추천 시스템이 연구되기 시작했다.

- Spark를 이용한 빅데이터

데이터에 대한 중요성을 인식하면서 많은 데이터를 DB에 적재하기 시작했다. 빅데이터가 됨에 따라 기존의 모델로는 추천하기에 한계가 있다는 것을 느꼈고, 따라서 빅데이터의 연구가 활발해졌다.

- 딥러닝을 이용한 추천 시스템

딥러닝의 발달에 힘입어 빅데이터 공간에서 기존에 연구했던 협업 필터링과 딥러닝을 조합하여 추천을 할 수 있을지에 대한 연구가 이루어졌다.

- 개인화 추천 시스템

Factorization, 강화 학습, RNN 등 여러 기술과 알고리즘이 개발됨에 따라 현재는 **초 개인화 추천 시스템**이 진행되고 있다.


이 중 몇 가지를 살펴보자.


---
## 연관 분석(Association Analysis).

룰(rule) 기반의 모델로서 상품과 상품 사이에 어떤 연관이 있는지 찾아내는 알고리즘이다. 연관은 2가지 형태가 있다.

1. 얼마나(frequent) 같이 구매 되는가?
2. A 상품을 구매하는 사람이 B 상품을 구매하는가?

위의 두 형태의 의미는 비슷해 보이지만 다르다. **1번은 단순히 두 상품을 같이 구매한 빈도를 측정**하는 것이라면, **2번은 B 상품을 구매하기 위해서는 A 상품을 구매했다는 조건이 있어야 한다.** 예를 들어, 아이패드와 아이패드 펜슬이 있다면 아이패드(A)를 구매했기 때문에 아이패드 펜슬(B)을 구매한다는 것이다.


---
### 규칙 평가 지표.

- 지지도 (Support)

<center><img src="{{site.baseurl}}/assets/images/rs_aa_support.png" /></center><br>

지지도는 **전체 거래 중에서 A 상품과 B 상품이 같이 구매된 거래의 수**이다. 빈도 또는 구성비가 높은 규칙을 찾거나 불필요한 연산들을 줄일 때 기준으로 사용한다.

- 신뢰도 (Confidence)

<center><img src="{{site.baseurl}}/assets/images/rs_aa_confidence.png" /></center><br>

신뢰도는 **A 상품이 구매되었을 때 B 상품이 추가로 구매될 확률**이다. 즉, 조건부 확률을 의미한다. 당연히 신뢰도가 높으면 유용한 규칙일 가능성이 높다고 할 수 있다. 아이템 집합 간의 연관성 강도를 측정하는 데에 사용한다.

- 향상도 (Lift)

<center><img src="{{site.baseurl}}/assets/images/rs_aa_lift.png" /></center><br>

향상도는 **A 상품을 구매할 때 B 상품도 구매하는지 서로 간의 연관성을 파악하는 비율**이다. 생성된 규칙이 실제로 효용가치가 있는지를 분석할 때 사용한다. 조건절과 결과절이 서로 독립일 경우와 비교해서 **두 사건이 동시에 얼마나 발생하는지** 그 비율로 나타낸다. 향상도의 값을 y라고 할 때,

    y > 1 이면 양의 상관관계
    y = 1 이면 독립관계
    y < 1 이면 음의 상관관계


---
### 규칙 생성.

이제 연관 분석이 어떻게 규칙을 생성하는지에 대해서 알아보자.

<center><img src="{{site.baseurl}}/assets/images/rs_aa_img1.png" style="zoom:80%;" /></center><br>

연관 분석은 위 그림처럼 A, B, C, D, E의 5가지의 아이템이 있을 때, 이 5가지를 통해서 만들 수 있는 모든 경우의 수를 탐색하여 지지도, 신뢰도, 향상도가 높은 규칙들을 찾아내는 방식이다.

상품이 5개일 때 전체 경우의 수는

    5C1 : 5
    5C2 : 10
    5C3 : 10
    5C4 : 5
    5C5 : 1

따라서, 모두 구매하지 않는 경우 1을 제외하고 31이다.

**이 31개의 경우에 대해서 지지도, 신뢰도, 향상도를 계산하여 값이 큰 순서대로 연관성이 높다는 것을 분석하는 것이 연관 분석의 핵심이다.**


---
### 문제점.

<center><img src="{{site.baseurl}}/assets/images/rs_aa_combination.png" style="zoom:40%;" /></center><br>

연관 분석에는 치명적인 문제가 존재하는데, 그것은 **아이템의 증가에 비해 경우의 수가 기하급수적으로 증가한다는 것이다.** 예를 들어, 아이템이 100개일 때 경우의 수는 1.26 * 10^30이다. 이것만 해도 천문학적인 수인데 유튜브의 영상 수나 음원 사이트의 음원 수에 적용하는 것은 현실적으로 불가능하다. 이 문제를 해결하기 위해서 등장한 것이 Apriori 알고리즘이다.


---
## Apriori Algorithm.

Apriori 알고리즘은 아이템 셋의 증가를 줄이기 위한 방법이다. 기본적인 아이디어는 "빈번한 아이템 셋은 하위 아이템 셋 또한 빈번할 것이다."이다. 즉, **"빈번하지 않은 아이템 셋은 하위 아이템 셋 또한 빈번하지 않다."라는 이론으로 아이템 셋의 증가를 줄이는 방법**이다.

<center><img src="{{site.baseurl}}/assets/images/rs_aa_apriori.png" /></center><br>

그림을 통해 쉽게 설명해 보자면 A, B, C, D, E 5개로 만들 수 있는 조합들이 있다. 그중 A와 B의 조합이 빈번하지 않기 때문에 해당 조합을 제거하고, 하위의 AB를 포함하는 모든 조합도 빈도가 적을 것이라는 이론을 적용하여 미리 제거를 통해 애초에 탐색되지 않도록 한다. 그런 다음 남은 조합들 중에서 빈도를 계산하도록 하여 아이템 셋이 증가하는 것을 줄여 나가는 것이다.


### Apriori 진행 순서.

1. k개의 item을 가지고 단일 항목 집단 생성 (one-item frequent set)
2. 단일 항목 집단에서 최소 지지도(support) 이상의 항목만 선택<br>
  *최소 지지도는 사용자의 hyperparameter로서 임의로 원하는 값을 정해준다.*
3. 2에서 선택된 항목만을 대상으로 2개 항목 집단 생성
4. 2개 항목 집단에서 최소 지지도 이상의 항목만 선택
5. 위의 과정을 k개의 k-item frequent set을 생성할 때까지 반복


### Apriori 장단점.

> 장점
- 원리가 간단하여 사용자가 쉽게 이해할 수 있고 의미를 파악할 수 있음
- 유의한 연관성을 갖는 구매패턴을 찾아줌

> 단점
- 데이터가 클 경우(item이 많은 경우) 속도가 느리고 연산량이 많음
- 실제 사용시 많은 연관상품들이 나타남
- 그리고 Apriori를 통해 나타난 연관성은 **상관관계를 의미할 수는 있지만 그것이 인과관계를 의미하지는 않는다.** 예를 들어, 아이패드와 애플펜슬을 구매했을 때 아이패드를 구매했기 때문에 애플펜슬을 구매한 것이지만 이러한 인과관계를 파악하기 어렵다.


### Apriori 실습 코드.

```python
import numpy as np
import pandas as pd

from mlxtend.preprocessing import TransactionEncoder
from mlxtend.frequent_patterns import apriori
```


```python
data = np.array([
    ['우유', '기저귀', '쥬스'],
    ['양상추', '기저귀', '맥주'],
    ['우유', '양상추', '기저귀', '맥주'],
    ['양상추', '맥주']
])
```


```python
te = TransactionEncoder()
te_arr = te.fit(data).transform(data)
df = pd.DataFrame(te_arr, columns=te.columns_)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>기저귀</th>
      <th>맥주</th>
      <th>양상추</th>
      <th>우유</th>
      <th>쥬스</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%time

apriori(df, min_support=0.5, use_colnames=True)
```

    Wall time: 7.98 ms
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>support</th>
      <th>itemsets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.75</td>
      <td>(기저귀)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.75</td>
      <td>(맥주)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.75</td>
      <td>(양상추)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.50</td>
      <td>(우유)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.50</td>
      <td>(맥주, 기저귀)</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.50</td>
      <td>(양상추, 기저귀)</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0.50</td>
      <td>(기저귀, 우유)</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.75</td>
      <td>(양상추, 맥주)</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.50</td>
      <td>(양상추, 맥주, 기저귀)</td>
    </tr>
  </tbody>
</table>
</div>




쥬스의 지지도는 최소 지지도보다 작기 때문에 쥬스가 포함된 조합들이 제거된 것을 확인할 수 있다.


## FP-Growth Algorithm.

FP-Growth는 Apriori의 **속도 측면의 단점을 개선**한 알고리즘이다. 원리는 비슷하지만 FP Tree라는 구조를 사용하여 빠른 속도를 가진다. 하지만 발생하는 아이템 셋(frequent item sets)을 찾는데는 좋지만 아이템간의 연관성을 찾는 것은 어렵다.


### FP-Growth 진행 순서.

1. 모든 거래를 확인하여, 각 아이템의 지지도(support)를 계산하고 최소 지지도 이상의 아이템만 선택
2. 모든 거래에서 빈도가 높은 아이템 순서대로 순서를 정렬
3. 부모노드를 중심으로 거래를 자식노드로 추가해주면서 tree를 생성
4. 새로운 아이템이 나올 경우에는 부모노드부터 시작하고, 그렇지 않다면 기존의 노드에서 확장
5. 위의 과정을 모든 거래에 대해 반복하여 FP Tree를 만들고 최소 지지도 이상의 패턴만을 추출

<center><h4>FP-Growth Tree</h4></center>
<center><img src="{{site.baseurl}}/assets/images/rs_aa_fp_tree.png" /></center><br>

위 그림에서 테이블을 보면, 각 아이템들의 지지도가 설정한 최소 지지도보다 작은 아이템들은 제거하고 남은 아이템들의 조합에서 빈도가 높은 순서대로 정렬했다. 그리고 Root노드를 시작으로 아이템의 거래 조합들을 부모노드와 자식노드의 조합으로 만들어 tree를 생성한다. 새로운 아이템이 나올 경우에는 부모노드가 되고{(f), (f, c), (f, c, a), ...} 그렇지 않으면 기존의 노드에서 확장을 한다.{(f, b), (f, c, a, b), ...} 이러한 과정을 모든 거래 조합에 반복하여 FP Tree를 만들고 지지도가 낮은 순서부터 시작하여 조건부 패턴(Conditional Pattern)을 설정하고 모든 아이템에 대해서 반복을 한 뒤, 설정한 조건부 패턴을 토대로 신뢰도나 지지도를 통해서 연관 규칙들을 찾게 된다.


### FP-Growth 장단점.

> 장점
  - Apriori보다 빠르다.<br>
  *DB를 두 번만 탐색하면 되기 때문*
  - 후보 Item sets을 생성할 필요 없이 진행 가능<br>
  *아이템을 추가하면서 진행하는 방식이기 때문*

> 단점
  - 역시 대용량의 데이터셋에서는 연산량이 커서 메모리를 효율적으로 사용하지 못함
  - Apriori에 비해서 설계하기 어려움
  - 지지도의 계산이 FP-Tree가 만들어지고 나서야 가능함

### FP-Growth 실습 코드.

```python
import numpy as np
import pandas as pd

from mlxtend.preprocessing import TransactionEncoder
from mlxtend.frequent_patterns import fpgrowth
```


```python
data = np.array([
    ['우유', '기저귀', '쥬스'],
    ['양상추', '기저귀', '맥주'],
    ['우유', '양상추', '기저귀', '맥주'],
    ['양상추', '맥주']
])
```


```python
te = TransactionEncoder()
te_ary = te.fit(data).transform(data)
df = pd.DataFrame(te_ary, columns=te.columns_)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>기저귀</th>
      <th>맥주</th>
      <th>양상추</th>
      <th>우유</th>
      <th>쥬스</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%time

fpgrowth(df, min_support=0.5, use_colnames=True)
```

    Wall time: 1.99 ms
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>support</th>
      <th>itemsets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.75</td>
      <td>(기저귀)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.50</td>
      <td>(우유)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.75</td>
      <td>(양상추)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.75</td>
      <td>(맥주)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.50</td>
      <td>(기저귀, 맥주)</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.50</td>
      <td>(양상추, 기저귀)</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0.50</td>
      <td>(양상추, 기저귀, 맥주)</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.50</td>
      <td>(우유, 기저귀)</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.75</td>
      <td>(양상추, 맥주)</td>
    </tr>
  </tbody>
</table>
</div>

추출된 규칙은 Apriori와 같고, 속도는 FP-Growth가 더 빠르다는 것을 확인할 수 있다.


---
*※ 참고*

[SKplanet Tacademy](https://www.youtube.com/c/SKplanetTacademy)