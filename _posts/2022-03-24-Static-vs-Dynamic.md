---
layout: single
title: "정적 vs. 동적 (Static vs. Dynamic)"
excerpt: "Static vs. Dynamic Training and Inference"
toc: true
toc_sticky: true
categories:
  - machine-learning
---

---
## 정적 vs. 동적 학습 (Static vs. Dynamic Training)

넓은 범위로, 모델을 학습시키는 두 가지 방법이 있다.
- **정적 모델(static model)**은 오프라인으로 학습된다. 모델을 딱 한 번 학습한 다음 잠시 동안 사용한다.
- **동적 모델(dynamic model)**은 온라인으로 학습된다. 데이터가 지속적으로 시스템에 입력되고 이 데이터를 지속적인 업데이트를 통해 모델에 통합한다.

다음의 요점이 정적 학습을 할 것인지, 동적 학습을 할 것인지 결정에 가장 중요한 특징이다.
- 정적 모델은 구축 및 테스트가 더 쉽다.
- 동적 모델은 변화하는 데이터에 적응한다. 세상은 변화무쌍한 곳이다. 작년 데이터를 기준으로 구축된 판매 예측은 내년 결과를 성공적으로 예측하기 어려울 것이다.

**데이터 세트가 시간이 지남에 따라 변경되지 않는 경우, 동적 학습보다 생성 및 유지 관리 비용이 적은 정적 학습을 선택하는 것이 효율적이다.** 그러나, 많은 정보 자료는 시간이 지남에 따라 변한다. 예를 들어, 해수면과 같이 일정하다고 생각되는 특성이 있는 것들도 포함된다. 따라서, **정적 학습을 하더라도, 입력 데이터에 대한 변경 사항을 계속 감시해야 한다.**

예를 들어, 사용자가 꽃을 살 확률을 예측하기 위해 학습된 모델이 있다고 가정하자. 시간 압박으로 인해, 모델은 7월과 8월의 꽃 구매 행동 데이터를 사용하여 한 번만 학습된다. 그리고 나서 모델은 생산 환경에서 예측을 제공하기 위해 보내지지만, 업데이트는 되지 않는다. 모델은 몇 달 동안은 잘 작동하지만, 발렌타인 데이 전후에는 끔찍한 예측을 할 것이다. 왜냐하면, 그 기간의 사용자의 행동이 극적으로 변하기 때문이다.


---
## 정적 vs. 동적 추론 (Static vs. Dynamic Inference)

다음 추론 전략 중 하나를 선택할 수 있다.

- **오프라인 추론(offline inference)**, MapReduce 또는 이와 유사한 것을 사용하여 일괄 처리에서 가능한 모든 예측을 수행한다. 그런 다음 예측을 SSTable 또는 Bigtable에 작성한 후 이를 은닉/조회 테이블에 제공한다.
- **온라인 추론(online inference)**, 서버를 사용한 주문형 예측을 의미한다.

> **오프라인 추론의 장단점**은 다음과 같다.
- 장점 
  - 추론 비용에 대해 크게 걱정할 필요가 없다.
  - 일괄 할당량 또는 일부 거대한 MapReduce를 사용할 수 있다.
  - 실제로 모델을 사용하기 전에, 예측에 대한 사후 검증을 할 수 있다.
- 단점
  - 알고 있는 것만 예측할 수 있다. 롱테일(long tail) 현상에는 좋지 않다.
  - 업데이트 지연 시간은 몇 시간 또는 며칠 단위로 측정될 수 있다.

> **온라인 추론의 장단점**은 다음과 같다.
- 장점
  - 새로운 아이템이 들어오는 대로 예측할 수 있다. 롱테일 현상에 적합하다.
- 단점
  - 계산이 집약적이고, 지연 시간에 민감하므로 모델 복잡성이 제한될 수 있다.
  - 모니터링 요구 사항은 더 집중적이다.


<br>
<br>
<br>

---
※ 참고

[Google Developer - Static vs. Dynamic Training](https://developers.google.com/machine-learning/crash-course/static-vs-dynamic-training/video-lecture)<br>
[Google Developer - Static vs. Dynamic Inference](https://developers.google.com/machine-learning/crash-course/static-vs-dynamic-inference/video-lecture)<br>