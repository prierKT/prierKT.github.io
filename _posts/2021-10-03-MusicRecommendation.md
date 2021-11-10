---
layout: single
title: "음원 특성 분석을 통한 음악 추천 AI"
excerpt: "콘텐츠 기반 필터링(Content-Based Filtering)과 시계열 분석 기반"
toc: true
toc_sticky: true
categories:
  - portfolio
---


프로젝트 기간 : 2021/05/24 ~ 2021/06/08



## 문제 인식 (개발 동기)
* 음악 스트리밍 서비스의 추천프로그램은 협업필터링(Collaborative Filtering)에 기반하여 곡의 재생 횟수와 ‘좋아요’를 얼마나 많이 받았는가에 따른 소비자의 선호도를 통계적인 방법으로 추천해주는 시스템이다.
  - 현재는 인공지능 기술이 급속도로 발전하면서, 인공지능을 통한 음악 추천프로그램을 서비스하는 기업이 증가하고 있다. (예: 멜론, 유튜브뮤직, 스포티파이 등)
  - 실제로 유튜브뮤직의 국내시장점유율이 급성장한 원인 중 개인 맞춤형 음악 추천 서비스의 높은 만족도가 크게 기여했다.


* 통계적인 추천방식은 상대적으로 재생 횟수나 선호도 반영이 적은 신곡이나, 발표된 시간이 꽤 지났더라도 빛을 보지 못한 이른바 ‘숨은명곡’에 대해서는 효과적인 추천이 이루어지지 않는다.


* 각각의 소비자의 취향을 분석한 것이 아니라, 서비스를 이용하는 모든 소비자에 대하여 통계를 산출한 것이기 때문에 소비자 개인의 취향을 반영할 수 없어 만족스러운 개인 맞춤형 서비스를 제공하기 힘들다.


* 따라서 **나의 취향에 맞춤인 곡을 검색하여 추천해 주는 인공지능**을 만들어 보기로 했다.



## 문제 해결 아이디어
**곡의 음원 특성들을 데이터화** 하여, 콘텐츠기반필터링(Content-Based Filtering)에 기반한 분석을 통하여 **곡의 분위기, 템포, 멜로디, 감정** 등이 유사한 곡을 정확하게 검색하여 추천함으로써 소비자에게 더 만족스러운 **개인 맞춤형 추천 서비스**를 제공할 수 있다.
* 아이디어에 기반하여 구축 할 모델의 기능은 다음과 같다.
  1. 좋아하는 노래의 좋아하는 부분을 녹음한다.

  2. 입력 받은 노래를 분석 및 전처리한다.

  3. 전처리된 데이터와 학습된 데이터를 비교한다.

  4. 비교한 결과에 따라 유사곡을 검색하여 추천한다.

  5. 녹음을 사용하지 않을 때를 대비하여, 그 날의 사용자의 기분에 따라 원하는 분위기의 곡을 들을 수 있도록 감성적인 라벨링을 통한 추천기능을 추가한다.


<center><p>[모델의 공정도]</p></center>
<center><img src="{{site.baseurl}}/assets/images/music-process.png" /></center><br>



## 개발 모델 설명
* 각 곡에 대한 음원 특성을 추출하여 푸리에 변환을 통해 데이터화
  - 음악적 분석을 위해 푸리에 변환을 통해 **시간이 도메인인 데이터를 주파수가 도메인인 데이터**로 변환하였다.


* 데이터 전처리 및 모델 학습에 사용하기 위해 데이터를 Database에 저장
  - **데이터를 효율적으로 관리하기 위해서 AWS(Amazon Web Service)를 이용하여 DB(Mysql)를 구죽**하여 진행했다. 음원 특성 데이터 추출은 **'librosa'**패키지를 사용하였고, 추출한 데이터는 다음과 같다.
    - Spectral Centroid : 곡의 중심을 나타내는 스펙트럼 형태의 측정 방법.
    - Spectral Rolloff : 곡의 주파수의 합이 저음인가를 측정하는 방법.
    - Zero-Crossing : 곡의 부드러움을 측정하는 방법.
    - Cromagram : 옥타브에 따라 다른 피치를 식별하는 측정 방법.
    - Tempo : 곡의 속도를 측정하는 방법.
    - Harmonic : 사람의 귀로 측정할 수 없는 특징들 (음악의 색깔).
    - Percussive Components : 곡의 리듬과 감정을 나타내는 충격파.
    - MFCC(Mel-Frequency Cepstral Coefficients) : 인간의 청각에 전달되는 주파수의 관점으로 신호를 측정하는 방법.


* DTW(Dynamic Time Warping)을 활용하여 시계열 분석
  - 음원은 시계열 데이터이다. 따라서 단순히 추출한 특성의 값을 분석하는 것뿐만 아니라, 시간에 따른 멜로디의 변화도 분석이 필요하다.
  - DTW는 두 개의 Sequence의 유사도를 분석하는 알고리즘이다.
  - 추출된 MFCC 값에 **DTW를 적용하여 시간이 다르더라도 곡의 변화 구간이 유사한 곡들의 DTW distance를 구해**, 비교가 가능하게 하였다.


<center><p>[유클리디안 vs DTW]</p></center>
<center><img src="{{site.baseurl}}/assets/images/DTW.png" /></center><br>


* 분석된 데이터들에 t-SNE(t-Stochastic Neighbor Embbeding)를 적용하여 차원축소 후, K-means Clustering으로 곡들의 유사도에 맞게 군집화
  - 데이터의 차원이 너무 크기 때문에, **차원의 저주에 빠지는 것을 막기위해 t-SNE를 적용**하였다.
  - 방대한 곡의 데이터들을 모두 일일이 비교하려면 연산량이 굉장히 커지고 그에 따라 시간도 오래 걸리기 때문에, K-means Clustering을 통해 elbow 기법을 적용하여 특성이 비슷한 곡들을 10개의 그룹으로 군집화하였다.


<center><p>[K-means Clustering 후 elbow 기법 적용 결과]</p></center>
<center><img src="{{site.baseurl}}/assets/images/k-means.png" /></center><br>


* 또한, 군집화된 곡들의 그룹은 입력된 곡(seed)이 없더라도, 각각의 군집에 대한 테마를 정하여 당시의 기분이나 분위기에 따라 소비자에게 감성적인 서비스를 할 수 있도록 하였다.


<center><p>[10개의 클러스터에 대한 감성 라벨]</p></center>
<center><img src="{{site.baseurl}}/assets/images/music-label.png" /></center><br>


* K-means Clustering을 통하여 지정된 label을 y값으로 지도학습
  - XGBoost와 DNN을 통한 학습을 진행하였는데, 큰 차이는 없었지만 DNN이 미세하나마 좋은 성능을 냈기 때문에 DNN으로 학습시킨 모델을 사용하였다.
    - XGBoost : accuracy = 0.98
    - DNN : accuracy = 0.99, loss = 0.03


<center><p>[DNN 학습 결과]</p></center>
<center><img src="{{site.baseurl}}/assets/images/DNN-result.png" /></center><br>


* 학습한 모델 테스트
  - 학습한 모델이 지정된 label을 정확하게 예측하여 return 하는지 테스트

<center><p>[예측결과 테스트]</p></center>
<center><img src="{{site.baseurl}}/assets/images/music-prediction.png" /></center><br>


## 모델 검증
* 학습이 끝난 모델을 사용하여 모델 검증 (아래의 첨부 영상 참고)
  - 입력 곡 : BTS - Butter (전체 구간 중 30초~36초 구간)
  - 예측 곡 : "엉덩이가 먼저 반응하게 되는 아이돌 노래 모음"
    - Christopher – Real Life
    - Maroon5 – Beautiful Mistakes
    - Anne Marie – Birthday
    - 오반 – 축하해 (Prod.by VAN.C)
    - CJ – Whoopty


<center><p>[텔레그램 봇 구현 영상]</p></center>
[<center><img src="https://img.youtube.com/vi/5rwFQe5uX9I/0.jpg" /></center>](https://www.youtube.com/shorts/5rwFQe5uX9I)


입력된 곡의 구간과 예측된 곡들의 분위기나 멜로디 변화가 유사하게 진행되는 곡이 성공적으로 예측되었다.<br><br>


---
## 모델 개선 방향 및 재고 사항
현재 모델은 유사곡을 찾기 위해 소비자가 맘에 드는 부분을 직접 녹음하여 검색해야 하는 방법이다. 이 방법이 소비자의 취향을 더 정확하게 반영할 수 있다는 장점이 있을 수 있으나, 녹음을 귀찮게 느끼거나 녹음을 사용할 수 없는 환경에 있는 소비자가 있을 수 있다. 따라서, 텍스트로도 곡을 입력하여 입력된 곡에 대한 유사곡을 추천해주는 기능이 추가된다면 활용도와 소비자 만족도가 더 좋을 것으로 예상된다.