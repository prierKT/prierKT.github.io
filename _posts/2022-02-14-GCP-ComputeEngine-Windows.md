---
layout: single
title: "Google Cloud Platform - 가상 머신 생성(Windows)"
excerpt: "Windows server, Remote Desktop Protocol"
toc: true
toc_sticky: true
categories:
  - etc
---


---
## Launch a Windows Server instance

가상 머신 서버를 윈도우로 만들어보자.

**VM instance**로 들어가서 **CREATE INSTANCE**를 클릭하고, 세부 사항을 설정하는 것은 [**이전 포스팅**](https://prierkt.github.io/etc/GCP-Creating-VM/)을 참고하면 된다.

Windows 서버를 만들기 위해서는 아래와 같이 **Boot disk** 하단의 **CHANGE**를 클릭히여 **Image**를 Linux에서 Windows로 바꿔주기만 하면 된다. 이게 끝이다. *참고로, linux보다 windows server가 더 비싸다.*

<center><img src="{{site.baseurl}}/assets/images/gcp_windows1.png" /></center><br>

모든 설정이 끝났으면 **CREATE**를 클릭하여 VM을 생성하자.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows2.png" /></center><br>


---
## Remote Desktop(RDP) into the Windows Server

Windows server가 생성되었으면, RDP를 연결해보자.

**RDP는 Remote Desktop Protocol의 약자로, 말 그대로 원격 접속을 할 수 있도록 도와주는 프로토콜이다.**

먼저, VM이 RDP 연결을 위한 준비가 되었는지를 확인하기 위해 **Cloud Shall**에서 명령어를 통해 확인해보자.
- **gcloud compute instances get-serial-port-output [VM 이름]**
  - 지역을 붇는 메세지가 나오면 **n**을 입력하고 **Enter**

<center><img src="{{site.baseurl}}/assets/images/gcp_windows3.png" /></center><br>

아래와 같은 메세지가 나와야 한다. 이는 OS 구성 요소가 초기화되었고, Windows server가 RDP 연결을 수락할 준비가 되었음을 의미한다. 만약 이렇게 나오지 않았다면, 이 메세지가 나올 때까지 반복하여 명령어를 입력한다.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows4.png" /></center><br>

위와 같이 준비가 되었다는 메세지가 나왔다면, 이제는 RDP에 로그인 하기 위한 비밀번호를 설정해야한다.
- **gcloud compute reset-windows-password [VM 이름] --zone [VM zone] --user [사용할 ID]**
  - `Would you like to set or reset the password for [admin] (Y/n)?`라는 비밀번호를 초기화 할 것이냐는 메세지인데, **Y**를 입력하고 **Enter**

<center><img src="{{site.baseurl}}/assets/images/gcp_windows5.png" /></center><br>

VM 목록으로 돌아가서, **Connect** 항목의 **RDP**를 클릭한다. 그러면, Chrome RDP 확장 프로그램을 설치하라는 메세지가 뜨는데 [Chrome RDP for Google Cloud Platform](https://chrome.google.com/webstore/detail/chrome-rdp-for-google-clo/mpbbnannobiobpnfblimoapbephgifkm) 크롬 공식 다운로드 링크이다. 설치하자.

RDP 확장 프로그램의 설치가 끝났으면, 이제 접속을 해보자. **RDP 옆의 화살표**를 클릭하면 다음과 같은 목록이 나오는데, **Download the RDP files**를 클릭하면,

<center><img src="{{site.baseurl}}/assets/images/gcp_windows6.png" /></center><br>

`.rdp`파일이 다운로드 된다.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows7.png" /></center><br>

다운로드 된 `.rdp`파일을 더블 클릭하여 실행하자. 아래와 같은 창이 열린다. **연결** 클릭.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows8.png" /></center><br>

**비밀번호**를 입력하고 **확인**을 클릭. 비밀번호는 아까 위에서 비밀번호를 초기화 할 때, shall에 초기화된 비밀번호가 생성되었다. 그것을 입력하면 된다.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows9.png" /></center><br>

정상적으로 비밀번호가 입력되었다면, 다음과 같은 창으로 넘어간다. 만약 계속 시도해도 로그인하지 못한다는 메세지가 나온다면, 다시 shall로 돌아가서 비밀번호를 한번 더 초기화를 하자. 그리고 나서 다시 시도를 하면 될 것이다. **예**를 클릭하면 본인이 생성한 Windows server로 연결된다.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows10.png" /></center><br>

다음과 같이 Windows 이미지가 나타나며 연결이 된다면 성공이다.

<center><img src="{{site.baseurl}}/assets/images/gcp_windows11.png" /></center><br>