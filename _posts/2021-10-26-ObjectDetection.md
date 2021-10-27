---
layout: single
title: "국방 학회 프로젝트"
excerpt: "객체 탐지 및 객체 추적 모델링"
toc: true
toc_sticky: true
categories:
  - portfolio
---


# Object Detection And Object Tracking Modeling
프로젝트 기간 : 2021/07 ~ 2021/09


## 1. 개요

국방 연구 관련 Object Detection 모델링에 관련된 프로젝트를 제안받았습니다. 국방 연구원들께 객체 탐지(Object Detection) 및 객체 추척(Object Tracking)교육을 해야하는데 그 강의에 쓰일 **Dataset 만들기 부터 학습한 모델로 객체 탐지 및 객체 추적**까지의 전체적인 과정들을 만들어 달라는 요청을 받았습니다. 요청받은 모델은 **우리나라의 주력전차(K1A1, K2)와 미국 (M1A2), 일본(Type 90)의 주력전차를 탐지하는 모델**입니다. 프로젝트를 진행하는 동안 여러 가지 이슈가 발생하였고, 그 이슈들을 해결해나가면서 프로젝트를 성공적으로 종료하였습니다. 제가 작업한 내용들은 각 내용마다 [코드-Github]로 링크시켜놓았으니 확인하실 수 있습니다.


## 2. 초기 모델

### 2-1. Dataset 만들기

시작부터 이슈가 발생했습니다. 학습에 쓰일 데이터는 클라이언트께서 보내주셨지만, 데이터의 질이 좋지 않아서 정확한 모델을 만들기에는 크게 역부족이었습니다. 자세히 설명드리며 여러 번 재요청을 했지만 만족스러운 데이터를 받을 수 없었습니다. 따라서 학습 데이터도 직접 만들어서 학습을 진행하였습니다.

초기 모델의 이미지 데이터셋을 만들기 위해서 *The Ai Guy*의 Github [OIDv4_Toolkit](https://github.com/theAIGuysCode/OIDv4_ToolKit)을 사용하였습니다. [Open Images Dataset V6](https://storage.googleapis.com/openimages/web/index.html)의 이미지들을 다운로드 해주는 툴이며, label 파일도 함께 다운로드 할 수 있기 때문에 오픈소스 이미지 데이터를 구하는데 유용한 툴입니다.



### 2-2. 모델링

#### 2-2-1. 모델 선택

학습에 사용한 프레임워크는 **YOLOv4**를 사용하였습니다. Google의 [Tensorflow Detection Model](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md)과 AlexeyAB의 [Darknet](https://github.com/AlexeyAB/darknet)을 Roboflow에서 제공하는 public datset 중 [Pistol 데이터](https://public.roboflow.com/object-detection/pistols)를 직접 학습시키며 비교해본 결과는,

* AP :
  * YOLOv4 : 88%
  * SSD ResNet152 V1 FPN 640x640 (RetinaNet152) : 80% [코드-Github](https://github.com/prierKT/tensorflow_detection_model/blob/main/TF2_SSD_resnet152_v1_fpn_640.ipynb)
  * EfficientDet-D3 896x896 : 81% [코드-Github](https://github.com/prierKT/tensorflow_detection_model/blob/main/EfficientDet-d3-TensorFlow2-Object-Detection.ipynb)

위 처럼 나왔으며, Object에 관한 오인식률도 가장 적고 Bounding Box(이 후 bbox로 칭함)의 범위도 Object의 경계선에 가장 가깝게 그려졌습니다. 따라서, 전체적으로 YOLOv4가 더 좋은 성능을 가지고 있다고 판단하여 결정하였습니다.



#### 2-2-2. 모델 학습 및 결과
학습에 사용한 데이터 : (num of train, num of test)

* Airplane : (4948, 1239)
* Tank : (1062, 166)
* Weapon : (1646, 386)

<center><img src="{{site.baseurl}}/assets/images/chart-ATW.png" style="zoom:50%;" /></center><br>


위의 차트는 학습한 결과입니다. `iteration : 6000`의 학습에서 가장 좋은 수치는 `mAP : 64%`, `loss : 약 1.4`가 측정되었습니다. 학습하는 동안의 loss를 모니터링하며 학습이 안정적으로 진행되었음을 확인할 수 있습니다.<br>

### 2-3. 모델 검증

#### 2-3-1. Object Detection
* K1A1
<center><img src="{{site.baseurl}}/assets/images/K1A1-ATW.gif" /></center><br>

* K2
<center><img src="{{site.baseurl}}/assets/images/K2-ATW.gif" /></center><br>

* M1A2
<center><img src="{{site.baseurl}}/assets/images/M1A2-ATW.gif" /></center><br>

* Type90
<center><img src="{{site.baseurl}}/assets/images/Type_90-ATW.gif" /></center><br>

학습을 완료하여 만들어진 모델을 사용하여 Object Detection한 결과 입니다. 최종적으로 만들어야 하는 모델은 전차를 구별할 수 있는 모델이므로, Tank 관련 영상을 구하여 객체 탐지를 실행하였습니다. 인식률 불안정, bbox 불안정, 원거리 탐지 불안정의 문제가 있었습니다. 그럼에도 학습시킨 데이터의 양과 질에 비해 어느 정도의 성능을 보이는 것을 확인할 수 있었으며, 충분히 전차들을 구별해낼 수 있는 모델을 만들 수 있다는 가능성을 가질 수 있었습니다.

#### 2-3-2. Object Tracking
* K1A1
<center><img src="{{site.baseurl}}/assets/images/K1A1-ATW-track.gif" /></center><br>

* K2
<center><img src="{{site.baseurl}}/assets/images/K2-ATW-track.gif" /></center><br>

* M1A2
<center><img src="{{site.baseurl}}/assets/images/M1A2-ATW-track.gif" /></center><br>

* Type90
<center><img src="{{site.baseurl}}/assets/images/Type_90-ATW-track.gif" /></center><br>

Object Tracking한 결과입니다. 모델의 탐지 성능이 불안정하기 때문에 객체 추적도 정확하게 하지 못하고 있는 것을 확인할 수 있었습니다.

## 3. 본 모델

### 3-1. Dataset 만들기
초기 모델을 학습시키면서 데이터의 중요성을 다시 한 번 깨닫게 되었습니다. 그래서 여러 방법으로 Dataset을 만들어 학습시켜보며, 모델이 가장 좋은 성능을 가진 최선의 Dataset을 만들었습니다. 클라이언트께 받은 각 전차들의 여러 영상을 활용하였습니다.

#### 3-1-1. 직접 Labeling하여 만들기
모든 데이터를 직접 Labeling 하였습니다. 여러 방법으로 Dataset을 만들어서 학습을 시켜 보았지만, 최강은 역시 장인 정신이었습니다. 작업에 필요한 이미지 데이터는 영상을 frame으로 나누어 저장하였습니다. 영상마다 각 전차들의 비슷한 장면이 겹치지 않으면서 최대한 다양한 장면을 확보할 수 있도록, 나눠야하는 frame step이 모두 다르기 때문에 **영상을 원하는 frame step 단위로 저장**하는 코드를 추가하였습니다. [코드-Github](https://github.com/prierKT/yolov4-custom-functions/blob/master/refine_data.py)

* Labeling은 **LabelImg**를 사용하여 작업하였습니다.

#### 3-1-2. Label 파일의 형식 바꾸기
학습을 시키기 위해서 label 파일을 학습하는 모델에 맞게 변환하는 과정이 필요합니다. label 파일에 저장되어 있는 bbox의 단순 좌표를 YOLO 형식에 맞게 변환하는 코드를 추가하였습니다. [코드-Github](https://github.com/prierKT/yolov4-custom-functions/blob/master/convert_yolo.py)

#### 3-1-3. Trainset, Testset으로 나누기
형식 변환까지 모든 작업이 끝난 데이터들을 랜덤으로 8:2로 나누어 Trainset과 Testset으로 나누어 주는 코드를 추가하였습니다. [코드-Github](https://github.com/prierKT/yolov4-custom-functions/blob/master/make_train_test.py)

### 3-2. 모델 학습 및 결과
여러 학습을 시켜보면서 default 설정의 학습보다는, hyperparameter의 조정을 통하여 더 좋은 성능을 낼 수 없는지 조사하고 연구한 결과 `.cfg` 파일의 hyperparameter의 수치를 조정함으로써 모델의 탐지 정확도와 bbox의 범위 정확도를 올릴 수 있었으며, 데이터의 특성에 따라 새로운 hyperparameter를 추가하거나 변경하여 최적화된 모델을 만들 수 있었습니다. [코드-Github](https://github.com/prierKT/yolov4-training-colab/blob/main/YOLOv4_Training.ipynb)

학습에 사용한 데이터 : (num of train images, num of test images)

* K1A1_K2 : (1628, 407)
* K2_K1A1 : (1042, 261)
* M1A2 : (1468, 368)
* Type90 : (1367, 342)

K1A1과 K2는 우리나라의 주력전차이기 때문에, 영상에 같이 등장하는 경우가 많습니다. labeling을 직접 정확하게 했기 때문에 굳이 나누는 것은 시간적으로 비효율적이라고 판단했기 때문에 나누지 않았습니다. **각 전차 데이터의 이미지 수와 bbox의 수는 큰 범위의 오차가 없도록** 비슷하게 맞추었습니다.

<center><img src="{{site.baseurl}}/assets/images/chart-Tank7.png" style="zoom:50%;" /></center><br>

학습한 결과 차트입니다. `iteration : 8000`의 학습을 진행하였고, `mAP : 99%`, `loss : 0.5 ~ 0.6`이 측정되었습니다. 상당히 높은 정확도를 가진 모델이 만들어 졌으며, loss를 통해 굉장히 안정적으로 학습을 한 것을 확인할 수 있습니다.

### 3-3. 모델 검증

#### 3-3-1. Object Detection
* K1A1
<center><img src="{{site.baseurl}}/assets/images/K1A1-Tank7.gif" /></center><br>

* K2
<center><img src="{{site.baseurl}}/assets/images/K2-Tank7.gif" /></center><br>

* M1A2
<center><img src="{{site.baseurl}}/assets/images/M1A2-Tank7.gif" /></center><br>

* Type90
<center><img src="{{site.baseurl}}/assets/images/Type_90-Tank7.gif" /></center><br>

Detection한 결과를 보면 높은 정확도로 근거리, 원거리의 Object를 탐지해내는 것을 확인할 수 있으며, K1A1과 K2가 한 장면에 같이 등장하더라도 구별해내는 것을 확인할 수 있습니다. 또한, bbox의 범위도 들쭉날쭉 하지않고 전차의 끝 부분에 알맞게 그려지는 것도 확인할 수 있습니다.

#### 3-3-2. Object Tracking
* K1A1
<center><img src="{{site.baseurl}}/assets/images/K1A1-Tank7-track.gif" /></center><br>

* K2
<center><img src="{{site.baseurl}}/assets/images/K2-Tank7-track.gif" /></center><br>

* M1A2
<center><img src="{{site.baseurl}}/assets/images/M1A2-Tank7-track.gif" /></center><br>

* Type90
<center><img src="{{site.baseurl}}/assets/images/Type_90-Tank7-track.gif" /></center><br>

Tracking한 결과도 역시 좋은 성능을 보이고 있는 것을 확인할 수 있습니다. 특히 K2의 결과에서 모델이 판단하여 같은 전차라고 판단했을 경우에는 Tracking number를 같은 번호로 표시를 해주고 있습니다.<br><br><br>


## 프로젝트를 마치며
이 프로젝트를 진행하면서 이슈가 발생했을 때의 대처능력을 많이 훈련할 수 있었던 것 같습니다. 여러 차례 시도해보며 막혔을 때, 본인 나름의 연구를 통해서 전보다 더 나은 모델을 만들어 나가는 방법을 어느 정도 깨우칠 수 있었던 프로젝트였습니다. 몰랐던 기술을 공부하고 연구하여 나의 것으로 만들고 이를 활용해 좋은 모델을 개발하는 과정이 너무 재미있었습니다.

위에서 기술한 내용 이외에도 제가 작업한 내용이 궁금하신 분들은 저의 [Github](https://github.com/prierKT)에서 확인하실 수 있습니다. 감사합니다.