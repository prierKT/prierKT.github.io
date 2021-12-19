---
layout: single
title: "한/미/일 주력 전차 탐지 모델"
excerpt: "객체 탐지 및 객체 추적 모델링"
toc: true
toc_sticky: true
categories:
  - portfolio
---

# Object Detection Modeling
의뢰주 : 주식회사 에이콘이즈<br>
프로젝트 기간 : 2021/07 ~ 2021/09


## 1. 개요 

단기 프로젝트를 제안받았습니다. 객체 탐지 관련 기술은 다뤄본 적이 없었기 때문에 새로운 기술을 접할 수 있는 기회라서 참여하였습니다. 국방 연구원들에게 교육 목적으로 진행되는 프로젝트인 만큼, 단순히 결과물 뿐만 아니라 오히려 과정에 더 초점이 맞춰진 프로젝트입니다.


## 2. 프로젝트 설명

- 내용 : 한국(K1A1, K2) / 미국(M1A2) / 일본(Type 90)의 전차를 탐지해 내는 모델 개발<br>
  - 데이터 셋 구축 ~ 모델 검증까지의 모든 과정
- 목적 : 국방 연구원들에게 객체 탐지 기술 교육


## 3. 프로젝트 진행


### 3-1. 데이터 셋 구축


#### 3-1-1. 데이터 수집

가장 중요하다고 생각하는 과정입니다. **모델의 성능에 가장 큰 영향을 미치는 것은 학습에 사용되는 데이터**라는 것을 다시 한번 느낄 수 있었습니다. 어떤 데이터 셋을 사용하여 학습을 시켜야 가장 좋은 성능을 가진 모델이 만들어지는지, 여러 시행착오를 겪으면서 다양한 방법으로 데이터 셋을 만들어 학습을 시켜 보았습니다.

1. 클라이언트에게서 제공된 데이터
  - 각 전차의 모형을 하나씩 만들어 위, 아래, 정면 세 각도에서 회전시키며 찍은 사진

2. 구글 이미지에서 크롤링한 데이터
  - 구글 이미지를 통해 각 전차의 이미지를 크롤링 후 정제한 데이터

3. 학습된 모델로 자동화하여 만든 데이터
  - 학습한 모델로 영상을 탐지시켜 탐지된 시점의 프레임과 Bbox의 좌표가 저장되도록 자동화하여 만든 데이터

4. 영상을 프레임으로 나누어 저장한 데이터
  - 각 전차들의 훈련 영상, 소개 영상 등 다양한 배경과 다양한 모습의 전차들이 찍힌 영상을 크롤링하여 프레임으로 저장 후 정제한 데이터

각각의 데이터 셋을 따로 학습 시켜보고, 데이터 셋끼리 조합해서 학습을 시켜 보았지만 **4번 데이터 셋을 사용한 모델이 가장 정확도가 높은 성능**을 가졌습니다. 3번의 경우, 더 연구한다면 충분히 좋은 모델을 만들어 낼 수 있는 가능성이 있다는 것을 알 수 있었습니다.


#### 3-1-2. 라벨링 작업

- 라벨링 작업은 LabelImg를 활용

다른 프레임워크의 모델을 시도하기 위해서는 라벨링 작업을 다시하거나, 유료 서비스를 이용하는 등 불필요한 시간과 비용이 들기 때문에 **단순 좌표가 저장된 `.txt` 형식의 label file을 `.xml` 또는 `yolo` 형식으로 변환해주는 코드를 작성하여 시간과 비용을 줄일 수 있었습니다.**


### 3-2. 모델 선정

- Tensorflow2 Detection Model
  - EfficientDet D3 896x896 (AP : 83%)
  - SSD MobileNet V2 FPNLite 640x640 (AP : 81%)
- Darknet
  - Yolov4 (AP : 86%)
  - Scaled Yolov4 (AP : 89%)

4개의 모델을 Roboflow에서 제공하는 오픈소스 데이터 셋 중 Pistol Dataset으로 학습을 시켜 본 결과, `Scaled Yolov4 > Yolov4 > EfficientDet > SSD` 순으로 AP가 높았습니다. 결과에 따라 Scaled Yolov4를 사용하기 위해 Yolov4를 사용하는 코드를 수정하는 작업을 했지만, 소스 코드를 해독하는 능력이 아직 부족하여 실패하였습니다. 아쉽지만 **최종적으로 Yolov4를 선택하여 진행하였습니다.**


### 3-3. 모델 학습

- 사용한 데이터 셋 - 4번(영상을 프레임으로 나누어 저장한 데이터)
  - 데이터 셋 (Num of Train : 5505, Num of Test : 1378)
    - K1A1 (1335, 334)
    - K2 (1335, 334)
    - M1A2 (1468, 368)
    - Type90 : (1367, 342)
  - *Bounding box의 수는 약 이미지 수 x 3*

- 학습 모델 : Yolov4

- 최종 학습 결과
  - Iteration : 8000
  - Time taken : 21h
  - Train Loss : 0.5587
  - mAP : **99%**

<center><p>[학습 시각화 차트]</p></center>
<center><img src="{{site.baseurl}}/assets/images/chart-Tank7.png" style="zoom:70%;" /></center><br>

위에서 언급했듯이 데이터 셋들을 활용하여 여러 방법으로 학습과 시각화를 시켜본 결과, 선명한 이미지, 흐릿한 이미지, 다양한 각도의 장면과 모습, 원거리와 근거리 등 **안정적인 학습에 데이터 셋이 얼마나 중요한 요소인지를 알 수 있었습니다.**


### 3-4. 모델 검증

학습 시킨 모델을 사용하여 테스트 영상에 객체 탐지를 구현한 결과입니다.

* K1A1
<center><img src="{{site.baseurl}}/assets/images/K1A1-Tank7.gif" /></center><br>

* K2
<center><img src="{{site.baseurl}}/assets/images/K2-Tank7.gif" /></center><br>

* M1A2
<center><img src="{{site.baseurl}}/assets/images/M1A2-Tank7.gif" /></center><br>

* Type90
<center><img src="{{site.baseurl}}/assets/images/Type_90-Tank7.gif" /></center><br>

더 정확한 모델을 만들 수 있는 방법을 연구하였습니다. 모델 자체의 한계는 있겠지만, 논문을 읽어 모델을 이해하고 공식 github에서 **configuration file의 파라미터를 핸들링하는 방법 등을 공부하여 다양한 시도를 통해 mAP와 Bbox의 정확도 및 정밀도를 향상시켜 좋은 결과를 얻을 수 있었습니다.**<br><br>

---
*※ 작업 코드 (Github)*<br>
*[학습(Using google colab)](https://github.com/prierKT/yolov4-training-colab)*<br>
*[모델 검증 및 활용](https://github.com/prierKT/yolov4-custom-functions)*<br>

*※ 참고*<br>
*[YOLOv4: Optimal Speed and Accuracy of Object Detection](https://arxiv.org/pdf/2004.10934)*<br>
*[Scaled-YOLOv4: Scaling Cross Stage Partial Network](https://arxiv.org/pdf/2011.08036)*<br>
*[Darknet-github](https://github.com/AlexeyAB/darknet)*<br>
*[TensorFlow2 Detection Model Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md)*<br>
*[TensorFlow2 Object Detection API tutorial](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/index.html)*<br>