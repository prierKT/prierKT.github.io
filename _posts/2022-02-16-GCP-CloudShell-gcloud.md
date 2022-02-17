---
layout: single
title: "Google Cloud Platform - gcloud"
excerpt: "Cloud Shell, Gcloud"
toc: true
toc_sticky: true
categories:
  - etc
---


---
**gcloud**는 Google Cloud용 명령어 도구이다. Cloud Shell에 사전 설치가 되어 있으며, tab-completion을 지원한다. tab-completion은, 예를 들어, compute라는 명령어를 입력할 때, com까지만 적고 tab키를 누르면 자동으로 뒤의 pute까지 적어줘서 명령어를 완성시키는 기능이다.

첫 포스팅에서 [gcloud를 사용하여 VM 생성](https://prierkt.github.io/etc/GCP-Creating-VM/#in-cloud-shall)에 대해 공부한 적이 있는데, 오늘은 그보다 더 자세하게 gcloud를 활용하는 방법을 알아보겠다.

---
## 환경 설정 (Configure environment)

조정할 수 있는 개발 환경의 측면에 대해 알아보자.


### Region and Zone

우리가 VM을 생성하는데 사용하는 Google Compute Engine의 리소스는 **region** 또는 **zone**이라는 영역에 있다. region과 zone은 특정 지리적 위치를 말한다. 각 region에는 하나 이상의 zone이 있다. 예를 들어, `region : us-central1`에는 `zone : us-central1-a, us-central1-b, us-central1-c, us-central1-f`이 있으며 이 위치는 미국 중부 지역을 나타낸다. 아래의 그림은 해당 region의 zone을 보여준다.

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud1.png" /></center><br>

zone에 있는 리소스를 **영역 리소스(zonal resources)**라고 한다. VM과 영구 디스크는 zone에 위치한다. 영구 디스크를 VM에 연결하기 위해서는 두 리소스가 동일한 zone안에 있어야 한다. 마찬가지로, VM에 고정 IP 주소를 할당하기 위해서는 VM이 고정 IP 주소와 동일한 region에 있어야 한다. 더 자세한 사항은 [Regions and Zones documentation](https://cloud.google.com/compute/docs/regions-zones/)을 확인하자.

VM을 생성할 때, 기본으로 설정되어있는 region 및 zone 설정이 어디로 되어 있는지 확인하려면 명령어를 실행하면 된다.

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud2.png" /></center><br>

- **gcloud config get-value compute/zone**
- **gcloud config get-value compute/region**

위의 사진처럼, **(unset)**이라는 출력이 나온다면 기본 region 또는 zone이 설정되지 않았음을 의미한다.

### 기본 region 과 zone 확인 (Identify your default region and zone)

- 먼저 본인의 project ID를 알아야 한다. 좌측 상단의 **'Google Cloud Platform'**을 클릭하고 바로 밑에 **'Project info'**에서 **'Project ID'**를 확인할 수 있다.

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud3.png" /></center><br>

- cloud shell에서 gcloud 명령어를 입력한다.
  - **gcloud compute project-info describe --project [your_project_ID]**

출력에서 default region 또는 zone의 메타데이터 값을 찾으면 된다. 만약 `google-compute-default-region` 또는 `google-compute-default-zone`의 키와 값이 없으면, 설정되어 있지 않다는 것이다.


### 환경 변수 설정 (Set environment variables)

환경 변수는 환경을 정의하고 API 또는 실행 파일이 포함된 스크립트를 작성할 때 시간을 절약하는데에 도움이 된다.

- Project ID를 저장할 환경 변수를 만들고 [your_project_ID]를 이전에 실행한 `gcloud compute project-info describe` 명령어의 `name` 값으로 바꾼다.
  - **export PROJECT_ID=[your_project_ID]**

- zone을 저장할 환경 변수를 만들고 [your_zone]을 이전에 실행한 `gcloud compute project-info describe` 명령어의 `zone` 값으로 바꾼다.
  - **export ZONE=[your_zone]**

변수가 올바르게 설정되었는지 확인하려면 다음의 명령어를 실행한다.
- **echo $PROJECT_ID**
- **echo $ZONE**

변수가 올바르게 설정되었다면, 프로젝트 ID와 zone을 출력할 것이다.

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud4.png" /></center><br>


### gcloud로 VM 생성 (Create a virtual machine with the gcloud tool)

Cloud Console을 사용하여 VM instance를 만드는 대신 Cloud Shell에서 gcloud를 사용할 수 있다. Cloud Shell은 필요한 모든 개발 도구(gcloud, git 등)가 적재된 Debian 기반 가상 머신이며 영구적으로 5GB의 홈 디렉토리를 제공한다. 더 자세한 사항은 [gcloud command line tool guide](https://cloud.google.com/sdk/gcloud/)에서 확인할 수 있다.

- 명령어를 통해 새 VM instance를 생성한다.
  - **gcloud compute instances create [VM 이름] --machine-type [사용할 성능] --zone $ZONE**

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud5.png" /></center><br>

#### Command details

- **gcloud compute**는 Compute Engine API 보다 간단한 형식으로 Compute Engine 리소스를 관리할 수 있다.
- **instances create**는 새 인스턴스를 생성한다.
- **[VM 이름]**은 VM의 이름이다.
- **--machine-type** 플래그는 머신 유형을 지정한다.
- **--zone**은 VM이 생성되는 위치를 지정한다.
- --zone 플래그를 생략하면 gcloud가 기본 속성을 기반으로 원하는 영역을 유추할 수 있다. --machine-type 및 image와 같은 기타 필수 설정값들은 create 명령에 지정되지 않은 경우, 기본값으로 설정된다.

*--는 -가 두 개이다. -는 -가 하나이다.*

- create 명령에 대한 도움말을 열려면 다음의 명령어를 실행하면 된다.
  - **gcloud compute instances create --help**

도움말 내용을 스크롤하려면 ENTER 또는 스페이스바를 누르면 된다. 종료하려면 q를 누르면 된다.

### gcloud 명령어 탐색 (Explore gcloud command)

gcloud 툴은 gcloud 명령어 끝에 -h 플래그를 추가하여 간단히 사용 설명을 볼 수 있다.

- **gcloud -h**

--help 플래그를 명령어에 추가하거나 gcloud help 명령어를 실행하여 더 자세한 도움말을 볼 수 있다.

- **gcloud config --help**

- **gcloud help config**

위의 두 명령어의 실행 결과는 동일하다.

gcloud [Global Flags](https://cloud.google.com/sdk/gcloud/reference/)는 호출 단계에서 명령어의 동작을 제어한다. 플래그는 SDK 속성에 설정된 모든 값을 재정의한다.

- 환경의 구성 목록을 확인.
  - **gcloud config list**

- 모든 속성과 해당 설정 확인
  - **gcloud config list --all**

- 구성 요소 리스트
  - **gcloud components list**


---
## 새 구성 요소 설치 (Install a new component)

gcloud를 더 쉽게 작업할 수 있도록 도와주는 구성 요소를 설치해보자.

### Auto-complete mode

`gcloud interactive`는 명령과 플래그에 대한 자동 프롬프트를 가지고 있으며, 명령어가 입력될 때 창의 아래쪽 섹션에 도움말 정보를 표시한다.

드롭다운 메뉴를 사용하여 명령어 및 하위 명령어, 플래그 값과 같은 정적 정보를 자동 완성할 수 있다.

- 베타 구성 요소를 설치한다.
  - **sudo apt-get install google-cloud-sdk**

- `gcloud interactive`를 활성화한다.
  - **gcloud beta interactive**

interactive 모드를 사용할 때, **tab**키를 눌러 파일 경로와 리소스 변수를 완성한다. 드롭다운 메뉴가 나타나면 **tab**키를 눌러 목록을 이동하고 스페이스 바를 눌러 선택 항목을 선택한다.

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud6.png" /></center><br>

다음 명령어를 자동 완성을 사용하여 입력해보자.
- **gcloud compute instances describe [VM 이름]**

Cloud Shell 창 아래에 단축키 목록이 표시된다. **F2**키는 도움말 섹션을 활성화 시킬 때 사용하며 **한 번 누르면 ON**, **한 번 더 누르면 OFF**로 전환된다.

**exit**을 입력하거나 **F9**키를 누르면 종료된다.


---
## SSH를 사용하여 VM에 연결 (Connect to VM instances with SSH)

`gcloud compute`를 사용하면 인스턴스에 쉽게 연결할 수 있다. `gcloud compute ssh`는 인증 및 인스턴스 이름을 IP 주소로 매핑을 처리하는 SSH 래퍼를 제공한다.

- SSH를 사용하여 VM에 연결한다.
  - **gcloud compute ssh [VM 이름] --zone $ZONE**


---
## 홈 디렉토리 사용 (Use the Hone directory)

홈 디렉토리를 사용해보자. Cloud Shell 홈 디렉토리의 내용은 VM이 종료되고 다시 시작된 후에도 모든 Cloud Shell 세션 사이의 프로젝트에서 유지된다.

- 현재 작업 디렉토리 변경
  - **cd $HOME**

- `vi` 텍슽 편집기를 사용하여 `.bashrc` 환경 설정 파일을 연다.
  - **vi ./.bashrc**

편집기가 열리고 파일의 내용을 보여준다.

<center><img src="{{site.baseurl}}/assets/images/gcp_gcloud7.png" /></center><br>

편집기를 종료하려면 **ESC** - **:wq** - **Enter** 순으로 입력하면 종료된다.