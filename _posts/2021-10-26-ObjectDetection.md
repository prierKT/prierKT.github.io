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

국방 연구 관련 Object Detection 모델링에 관련된 프로젝트를 제안받았습니다. 국방 연구원들께 객체 탐지(Object Detection) 및 객체 추척(Object Tracking)교육을 해야하는데 그 강의에 쓰일 **Dataset 만들기 부터 학습한 모델로 객체 탐지 및 객체 추적**까지의 전체적인 과정들을 만들어 달라는 요청을 받았습니다. 요청받은 모델은 **우리나라의 주력전차(K1A1, K2)와 미국 (M1A2), 일본(Type 90)의 주력전차를 구별해 낼 수 있는 모델**입니다. 프로젝트를 진행하는 동안 어려가지 이슈가 발생하였고, 그 이슈들을 해결하기 위해서 소스코드를 수정 및 추가하면서 프로젝트에 맞게 커스터마이징하여 해결하였습니다. 그 결과 프로젝트를 성공적으로 종료하였습니다.


## 2. 초기 모델

### 2-1. Dataset 만들기

시작부터 이슈가 발생했습니다. 학습에 쓰일 데이터는 클라이언트께서 보내주셨지만, 데이터의 질이 좋지 않아서 정확한 모델을 만들기에는 크게 역부족이었습니다. 자세히 설명드리며 여러 번 재요청을 했지만 만족스러운 데이터를 받을 수 없었습니다. 따라서 학습 데이터도 직접 만들어서 학습을 진행하였습니다.

초기 모델의 이미지 데이터셋을 만들기 위해서 *The Ai Guy*의 Github [OIDv4_Toolkit](https://github.com/theAIGuysCode/OIDv4_ToolKit)을 사용하였습니다. [Open Images Dataset V6](https://storage.googleapis.com/openimages/web/index.html)의 이미지들을 다운로드 해주는 툴이며, label 파일도 함께 다운로드 할 수 있기 때문에 오픈소스 이미지 데이터를 구하는데 유용한 툴입니다.



### 2-2. 모델링

#### 2-2-1. 모델 선택

학습에 사용한 프레임워크는 YOLOv4를 사용하였습니다. Google의 [Tensorflow Detection Model](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md)과 AlexeyAB의 [Darknet](https://github.com/AlexeyAB/darknet)을 Roboflow에서 제공하는 public datset 중 [Pistol 데이터](https://public.roboflow.com/object-detection/pistols)를 직접 학습시키며 비교해본 결과는,

* AP :
  * YOLOv4 : 88%
  * SSD ResNet152 V1 FPN 640x640 (RetinaNet152) : 80% ([코드-Github](https://github.com/prierKT/tensorflow_detection_model/blob/main/TF2_SSD_resnet152_v1_fpn_640.ipynb))
  * EfficientDet-D3 896x896 : 81% ([코드-Github](https://github.com/prierKT/tensorflow_detection_model/blob/main/EfficientDet-d3-TensorFlow2-Object-Detection.ipynb))

위 처럼 나왔으며, Object에 관한 오인식률도 가장 적고 Bounding Box(이 후 bbox로 칭함)의 범위도 Object의 경계선에 가장 가깝게 그려졌습니다. 따라서, 전체적으로 YOLOv4가 더 좋은 성능을 가지고 있다고 판단하여 결정하였습니다.



#### 2-2-2. 모델 학습 및 결과

학습에 사용한 데이터 : (num of train, num of test)

* Airplane : (4948, 1239)
* Tank : (1062, 166)
* Weapon : (1646, 386)

<center><img src="{{site.baseurl}}/assets/images/chart-ATW.png" style="zoom:50%;" /></center><br>


위의 차트는 학습한 결과입니다. `iteration : 6000`의 학습에서 가장 좋은 수치는 `mAP : 64%`, `loss : 약 1.4`가 측정되었습니다. 학습하는 동안의 loss를 모니터링하며 학습이 안정적으로 진행되었음을 확인할 수 있습니다.<br>

* K1A1
<center><img src="{{site.baseurl}}/assets/images/K1A1-ATW.gif" style="zoom:50%;" /></center><br>

* K2
<center><img src="{{site.baseurl}}/assets/images/K2-ATW.gif" style="zoom:50%;" /></center><br>

* M1A2
<center><img src="{{site.baseurl}}/assets/images/M1A2-ATW.gif" style="zoom:50%;" /></center><br>

* Type90
<center><img src="{{site.baseurl}}/assets/images/Type_90-ATW.gif" style="zoom:50%;" /></center><br>

학습을 완료하여 만들어진 모델을 사용하여 Object Detection한 결과 입니다. 최종적으로 만들어야 하는 모델은 전차를 구별할 수 있는 모델이므로, Tank 관련 영상을 구하여 객체 탐지를 실행하였습니다. 인식률 불안정, bbox의 범위 불안정, 원거리 탐지 불가의 문제가 있었습니다. 그럼에도 학습시킨 데이터의 양과 질에 비해 어느 정도의 성능을 보이는 것을 확인할 수 있었으며, 충분히 전차들을 구별해낼 수 있는 모델을 만들 수 있다는 가능성을 가질 수 있었습니다.


## 3. 본 모델

### 3-1. Dataset 만들기
초기 모델을 학습시키면서 역시 데이터의 중요성을 다시 한 번 깨닫게 되었습니다. 그래서 가장 심혈을 기울인 부분이 바로 이 Dataset 입니다. 클라이언트께 받은 각 전차들의 여러 영상을 활용하여 Dataset을 만들었습니다.

#### 3-1-1. 초기 모델을 활용하여 만들기
AiGuy의 [yolov4-custom-function](https://github.com/theAIGuysCode/yolov4-custom-functions)에 crop 기능을 활용하였습니다. 이 기능은 학습한 모델로 Detection을 실행하면, Detection시에 그려지는 bbox에 맞게 object를 잘라내어 캡쳐하는 기능입니다. 이 기능의 코드를 수정 및 추가하여 사용하였습니다.

**작업 내용은 저의 [Github](https://github.com/prierKT/yolov4-custom-functions)에서 확인하실 수 있습니다.**

