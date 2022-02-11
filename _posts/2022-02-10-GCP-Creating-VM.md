---
layout: single
title: "Google Cloud Platform - 가상 머신 생성하기"
excerpt: "Cloud Consol, Cloud Shall"
toc: true
toc_sticky: true
categories:
  - etc
---

## 프로젝트 생성

가상 머신(Vitual Machine)을 생성하기 전에, 가장 먼저 해야할 것은 프로젝트를 생성하는 것이다.

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm0.png" /></center><br>

위의 사진을 보면 **'Google Cloud Platform' 바로 우측**에 점 3개와 'practice'라고 적혀있다. 'practice'부분은 현재 사용 중인 프로젝트 이름이다. 프로젝트 이름은 본인이 알아서 정하면 된다. 이를 클릭하면 프로젝트를 새로 생성하거나 이미 만들어 놓은 프로젝트들 중에서 사용할 프로젝트를 고를 수 있는 창이 뜬다.

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm2.png" /></center><br>

**'NEW PROJECT'를 클릭하여 새 프로젝트를 생성**하면 된다. 'ALL' 탭을 클릭하면 내가 만든 모든 프로젝트들을 확인할 수 있다.


---
## 가상 머신 생성

프로젝트를 생성했으면 이제 가상 머신을 생성해보자.


### In Cloud Console

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm1.png" /></center><br>

위의 사진에서 보는 것과 같이,
- **메뉴 버튼 - Compute Engine - VM instances** 에 접속하면,

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm3.png" /></center><br>

이렇게 생성할 수 있는 버튼이 나온다. 당연히 **'CREATE INSTANCE'** 클릭하자. *참고로 나는 언어 설정이 영어이다.*

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm4.png" /></center><br>

- **Name** : 생성하는 VM의 이름
- **Region** : 사용할 서버의 위치 (전 세계의 구글 클라우드 센터를 고를 수 있다.)
- **Zone** : Region 안에서 좀 더 들어간다고 생각하면 된다. Region이 광역시라면 Zone은 구라는 느낌이다.
- Machine configuration : 내가 생성할 VM의 성능이다.
  - **Series, Machine type**을 설정하여 세부 스펙을 설정하면 된다.

*참고로 우측에는 지금 설정되어 있는 성능으로 사용할 시에 발생하는 월요금이 계산되어 나온다. 바로 밑에 줄에는 시간 당 발생하는 요금까지 계산해준다. 구글 클라우드는 사용하는 동안에만 비용이 발생되므로 사용하지 않을 때는 꼭 비활성화 시키는 것을 잊지 않도록 하자. 까먹고 있다가 난데없는 요금 폭탄을 맞을 수도 있다.*

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm5.png" /></center><br>

- **Book disk** : 사용할 운영 체제에 대한 사항을 설정한다. (Redhat, Ubunto, Windows 등)
- **Identify and API access** : VM을 통해 API를 사용할 때의 접근 권한에 대한 설정이다.
- **Firewall** : 외부로부터의 접속에 대한 방화벽 설정이다. Http, Https에 대한 접속 허가를 설정한다.

설정이 끝났으면 맨 하단의 **'CREATE'** 버튼을 클릭하면 조금의 대기 시간 후 가상 머신이 생성된다.


#### SSH 연결 및 웹 서버 설치

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm6.png" /></center><br>

이제 생성된 VM을 통해 SSH에 연결하고 웹 서버를 설치해보자. 웹 서버는 Nginx를 설치할 것이다. 설치된 VM 목록의 우측에 **SSH** 클릭.

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm7.png" /></center><br>

ssh에 연결된 창이 뜬다. 관리자 계정으로 접속.
- **sudo su -**

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm8.png" /></center><br>

운영 체제(OS) 업데이트.
- **apt-get update**

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm9.png" /></center><br>

Nginx 설치.
- **apt-get install nginx -y**

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm10.png" /></center><br>

Nginx가 제대로 구동하는지 확인.
- <b>ps auwx | grep nginx</b>

눈으로 직접 확인하기 위해,
- 콘솔의 VM 목록으로 돌아가서, SSH옆에 VM의 **'External IP 주소'**를 클릭하거나,
- 터미널에 **http://EXTERNAL_IP/** 입력

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm11.png" /></center><br>

브라우저에 다음과 같은 화면이 뜨면 성공이다.



---
### In Cloud Shall

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm12.png" /></center><br>

쉘에서도 명령어를 통해 가상 머신을 설치할 수 있다. 우측 상단에서 **'Activate Cloud Shall'** 버튼을 클릭하면 클라우드 쉘에 연결된다.

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm13.png" /></center><br>

브라우저의 하단에 위와 같이 쉘에 연결된다.

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm14.png" /></center><br>

VM을 만들기 전에, 명령어를 통해 계정을 확인해보자.
- **gcloud auth list**

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm15.png" /></center><br>

프로젝트의 리스트도 확인할 수 있다.
- **gcloud config list project**

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm16.png" /></center><br>

명령어를 통해 VM을 생성할 때, 콘솔에서 설정한 사항들을 명령어를 통해 설정할 수 있다.
- **gcloud compute instances create [VM 이름] --machine-type [성능] --zone [사용할 서버 위치]**

명령어에 대한 자세항 사항은 [google command line](https://cloud.google.com/sdk/gcloud/)을 참고하자.

#### SSH 연결

<br><center><img src="{{site.baseurl}}/assets/images/creating_vm17.png" /></center><br>

ssh 역시 명령어를 통해 연결이 가능하다.
- **gcloud compute ssh [VM 이름] --zone [사용하는 서버 지역]**

명령어 입력 후 계속 진행하길 원하는 질문이 나오면 **y**, 그리고 암호키를 생성할 수 있는 칸이 나오는데 생성해도되고, 엔터치고 그냥 넘어가 생성하지 않아도 된다.

ssh 접속이 성공적으로 되었으면, 웹 서버 설치는 위에 Nginx를 설치할 때와 동일하게 진행하면 된다.