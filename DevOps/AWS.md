## 클라우드 컴퓨팅
- 공유 가능한 컴퓨팅 리소스를 어디서나 간편하게 요청에 따라 네트워크를 통해 접근할 수 있도록 하는 모델
  - 주문형 서비스
  - 넓은 범위의 접속 가능
  - 리소스의 공유
  - 신속한 확장성
  - 측정 가능

### 등장 배경
  - CPU 처리의 고속화
  - 빨라진 네트워크 속도와 거대해진 데이터 센터
  - IT 투자 비용의 절감과 효율성
  - 빠른 확장성

### On-Premise vs Cloud
|     | On-Premise    | Cloud    |
| ------- | ------- | ------- |
| 구축 속도 | 하드웨어와 소프트웨어를 조달해서 </br>배치, 설치하는데 시간이 오래 걸림 | 클라우드 서비스를 통해 빠르게 구축 가능 |
| 경제성 | 피크 타임의 이용량을 예상하여 </br>하드웨어와 소프트웨어 구입, </br>유지보수 인력 필요 | 사용하고자 하는 기간만 서비스를 사용, </br>소프트웨어와 데이터를 클라우드에서 통합관리 가능 |
| 유연성 | 서버 증축 및 확장에 많은 비용과 </br>고도의 기술이 들어감 | 필요한 만큼 확장과 축소가 쉽게 가능 |
| 가용성 | 자체적으로 백업 서버 및 </br>가용 영역을 구축해야 함 | 일부 하드웨어의 장애가 있더라도 </br>서비스를 계속 사용할 수 있도록 구성 |

### [클라우드 서비스 종류](https://www.redhat.com/ko/topics/cloud-computing/iaas-vs-paas-vs-saas)
![클라우드 서비스 종류](/DevOps/images/cloud-service.png)
#### IaaS (Infra as a Service) - host
- 가상 머신, 스토리지, 네트워크 등 인프라 리소스를 서비스 형태로 제공
- Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP)

#### PaaS (Platform as a Service) - build
- 개발 환경과 관련한 서비스 제공
- AWS Elastic Beanstalk, Heroku

#### SaaS (Service as a Service) - consume
- 모니터링이나 접근제어와 같은 소프트웨어 성격의 서비스 제공
- Dropbox, Salesforce, Google Apps

# Amazon Web Service
- 아마존에서 제공하는 클라우드 서비스
- 네트워크를 기반으로 가상 컴퓨팅 머신, 저장소, 데이터베이스 등 클라우드 서비스 제공
- 서비스 개발하고 배포하는데 쉽게 확장할 수 있도록 제공
- 소규모 스타트업 기업 부터 대규모 엔터프라이즈 기업까지 사용

## 특징
### 비교적 저렴한 비용 
- 종량 과금제 방식으로 시간대 별로 자원을 키거나 끄거나 할 수 있음
- 서비스의 성능을 변경하여 요금 절약 가능
### 유연성
- 몇 분 만에 전 세계 배포 가능, 언어 및 플랫폼에 구애받지 않음
### 즉시 확장성 
- 필요한 양의 리소스를 간단하게 증설 가능
### 가용성
- 각 region은 독립적으로 구성되어 있고, 동일 region에도 여러 AZ에 걸쳐 인프라 구성 가능

## AWS Region & AZ
### Region
- AWS가 전 세계에서 데이터 센터를 클러스터링 하는 물리적 위치
- Region은 지리적 영역 내 격리되고 물리적으로 분리된 여러 AZ로 구성

### Availabilty zone
- 높은 대역폭, 지연 시간이 짧은 네트워킹, 완전한 중복성을 갖춘 전용 메트로광 네트워크와 상호 연결
- AZ 간 100km 이내 물리적 분리

### Edge Location
- 정적 컨텐츠를 빨리 제공받을 수 있도록 CDN(Content Delivery Network)의 캐시서버의 모음
- 전세계 210개 이상의 Edge Location 존재

## AWS Services
### IAM (Identity and Access Management) 
- AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 웹 서비스

### EC2 (Elastic Compute Cloud)
- AWS 클라우드의 가상 서버

### ELB (Elastic Load Balancing)
- 하나 이상의 가용 영역에 있는 여러 대상 및 가상 어플라이언스 전반에서 수신되는 애플리케이션 트래픽을 자동으로 분산 

### S3
- 클라우드 스토리지 서비스
- 스토리지 관리 및 모니터링

### CodeBuild
- 소스 코드를 컴파일하고 테스트를 실행한 다음 바로 배포 가능한 소프트웨어 패키지를 생성할 수 있는 완전관리형의 지속적 통합 서비스

### CodeDeploy
- EC2 인스턴스, 온프레미스 인스턴스, 서버리스 Lambda 함수 또는 Amazon ECS 서비스로의 애플리케이션 배포를 자동화하는 배포 서비스

### CodePipeLine
- 소프트웨어를 릴리스하는 데 필요한 단계를 모델링, 시각화 및 자동화하는 데 사용할 수 있는 지속적 전달 서비스

# AWS 서비스와 활용
### 사전 작업
#### Github Repository 생성
- AWS 서비스에서 빌드에 활용하기 위해 원격 저장소 필요

#### AWS 회원가입
- 가입 시점으로부터 12개월 간 일정 사용량 무료
- 해외 결제 가능 카드 필요
- 가입 이후 root 계정에 MFA(Multi Factor Authentication) 인증 활성화

#### Admin 계정 생성
- AWS에서는 되도록 Root 계정 사용하지 않기를 권장
  - 약간의 제약이 있는 Administrator 권한을 가진 사용자 계정 생성
  - 실습에서 사용자는 admin 계정으로만 접근

#### IAM
- User 생성 - 권한(Administrator) 생성
- access key, private key download
- 비밀번호 생성 & MFA(Multi Factor Authentication) set up
- IAM user로 로그인

## S3
#### AWS에서 제공하는 인터넷용 스토리지 서비스
  - 단순한 웹 서비스 인터페이스
  - 높은 확장성과 신뢰성
  - 단독으로도 사용 가능하며 다른 서비스와 사용 가능(분석, 백업, 컨텐츠 배포 등)
  - HTTPS를 사용해 암호화 되어 있고, 데이터 자체를 암호화 가능
#### 개념
  - **객체** : S3에 저장되는 데이터(하나의 파일)
  - **버킷** : 객체가 파일이라면, 버킷은 객체를 묶은 최상위 디렉토리
  - **버전 관리** : S3에 저장되는 데이터의 변경 저장
  - **ACL** : 버킷이나 객체에 대해 요청자의 권한 허용 범위를 어디까지 설정할 것인가에 대해 간단하게 설정
  - **Glacier** : 빙하. S3에서는 잘 사용되지 않는 데이터를 보관하기 위해 Glacier로 만들어 매우 싼 가격으로 저장 가능
#### How to apply?
  - 빌드 결과물(아티팩트) 저장 & EC2에서 내려받아 사용
  - CodeBuild -> S3
  - S3 -> EC2
  - 따라서 외부에서 접근할 필요 X

## IAM
#### AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 웹 서비스
- IAM을 사용하여 AWS 기본 접근 및 리소스를 사용하도록 권한 부여
- Group, User, Role, Policy로 구성
- AWS Cli를 사용할 때, User에서 credential을 생성하여 사용
#### 구성요소
- User : AWS 내에서 생성하는 사용자로 AWS와 상호작용하는 사용자 혹은 어플리케이션 의미
- Group : IAM User의 집합, Group을 사용하여 다수 사용자에 대해 동일한 권한을 보다 쉽게 관리
- Role : 특정 권한을 가진 IAM 자격 증명
- IAM Policy : AWS의 접근하는 해당 권한을 정의하는 개체로 AWS IAM 리소스들과 연결하여 사용
#### How to apply?
- CodeDeploy 등 AWS  서비스에서 사용할 Role과 EC2에서 S3에 접근하기 위한 Role을 생성하여 사용
- 서비스에 사용되는 Role은 미리 정의된 Policy 적용
- EC2에 사용되는 Role은 필요한 Policy만 적용하여 사용

## EC2
#### 독립된 가상 서버를 빌려 사용하는 서비스
- 인스턴스 타입에 따라 성능을 다르게 사용 가능(메모리 최적, 컴퓨팅 최적, GPU...)
- Linux/windows 등 운영체제 선택 가능
- 사용한 만큼 요금 지불
- Elastic Beanstalk, Lambda 서비스도 내부적으로 EC2 사용
#### 구성요소
- **AMI(Amazon Machine Image)**
  - AWS에서 사용하는 미리 구성된 운영체제
- **가상 서버 사양**
  - vCPUs, 메모리, 스토리지(EBS), 네트워크 성능 등을 선택
- **Security Group**
  - 방화벽과 유사한 기능을 하는 기능
  - 들어오는 트래픽을 Inbound, 나가는 트래픽을 Outbound 룰로 제한할 수 있음
- **SSH Key-pair**
  - EC2 접근 시, SSH를 이용해 접근할 때 생성한 Key-pair의 pem키로 접근
#### How to apply?
- 아티팩트가 저장된 S3에서 아티팩트를 가져와 실행
- 실제로 서비스가 실행되는 가상 머신
- IAM에서 만든 Role을 사용해 S3에 접근
- CodeDeploy가 Agent를 통해 배포할 수 있도록 함

## ELB
#### 두 개 이상의 AZ에 EC2나 컨테이너, IP 주소 등으로 여러 대상에 걸쳐 수신되는 트래픽을 분산하는 서비스
- 접속 부하에 맞게 자동적으로 리소스의 확장/축소 수행
- Alb의 경우, 지속적으로 IP 주소가 바뀌며 IP 고정이 불가능하여 항상 도메인 기반으로 사용
- LB가 등록된 대상의 상태 모니터링 -> 정상인 대상으로 트래픽 라우팅
#### 구성요소
- **Listener** : 프로토콜과 포트를 기반으로 요청 받아 타겟으로 전달
- **Target Group** : ELB가 분산할 때 어디로 분산할 것이냐를 모은 그룹들
- **Security Group** : 방화벽과 같이 특정 프로토콜/포트만 허용하도록 하는 기능
- **Health Check** : 직접 트래픽을 발생시켜 인스턴스가 살아있는지 체크
- **Connection Time out** : 일정 시간 동안 통신 없을 때 커넥션 삭제할 것인가
#### 종류
- **CLB (Classic Load Balancer)**
  - 초기 형태의 LB, 서버의 기본주소 변경 시 재생성, 데이터 수정/변경 불가
- **NLB (Network Load Balancer)**
  - OSI 4계층에서 동작
  - TCP/UDP를 사용하는 요청에 이용
  - IP 고정 및 단순 라우팅/극도로 트래픽이 많은 경우 주로 사용
- **ALB (Application Load Balancer)**
  - OSI 7계층에서 동작
  - HTTP/HTTPS 지원
  - path 기반 라우팅
- **GWLB(GateWay Load Balancer)**
  - OSI 3계층에서 동작
  - 방화벽, 침입 탐지 및 방지 시스템
  - 패킷 검사
#### How to apply?
- 외부로부터 ELB로 들어오는 요청을 EC2로 전달하여 처리
- Health check를 통해 instance 정상 여부 체크
- 특정 포트로만 들어올 수 있도록 허용

# AWS CodeSeries
- CI/CD의 자동화를 위해 AWS에서 코드 저장소부터 배포까지 해주는 서비스들을 통칭해서 부르는 용어

## CodeCommit
- AWS에서 제공하는 Github와 같은 VCS를 위한 서비스
- 레포지토리의 소스 코드 암호화 가능
- IAM, CloudTrail 등과 같은 서비스를 연동해 레포지토리 액세스 및 제어 가능

## CodeBuild
- CI를 위해 소스코드로부터 **아티팩트를 생성**하기 위한 서비스
- CodeCommit이나 Git을 Target으로 함
- 빌드 서버를 직접 생성하거나 관리할 필요가 없음
- Buildspec.yml을 통해 빌드 순서 지정
- 빌드 머신을 지정할 수 있고, 사용한 빌드 시간만큼 요금 지불
### 구성
- **Source** : 빌드 대상이 될 소스 코드 레포지토리 설정
- **Environment** : 빌드할 환경(VPC, compute type, Docker image 등)을 선택
- **Buildspec** : 빌드 시점에 사용될 스크립트 지정
- **Artifacts** : 빌드 후 결과물 어디에 저장할 지 지정
- **Logs** : 빌드 과정을 Cloudwatch에 남기거나 혹은 S3에 저장할 지 지정
#### How to apply?
- Gibhub으로부터 Source code를 가져와 buildspec.yml에 정의된 빌드 스크립트 실행 후 S3에 빌드 결과물 저장
- 빌드 결과물은 war
- Deploy에 사용될 shell script와 appspec.yml을 zip으로 저장
- [buildspec.yml](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/build-spec-ref.html) - 프로젝트 root 디렉토리
  ```shell
  version: 0.2 # 사용 중인 빌드 사양 표준의 버전

  phases: # 명령을 실행하도록 지시할 수 있는 빌드 단계
    install:
      runtime-versions:
        java: corretto11
    pre_build:
      commands:
        - echo Nothing to do in the pre_build phase...
    build:
      commands:
        - echo Build started on `date`
        - mvn install
    post_build:
      commands:
        - echo Build completed on `date`
  artifacts:
    files:
      - build/libs/messageUtil-1.0.jar
    discard-paths: yes
  ```
## CodeDeploy
- 배포하고자 하는 App을 Deployment 그룹으로 **배포**하는 서비스
- 배포 진행상황 모니터링 가능
- AWS 서비스 사용 시 요금 청구 X
- BlueGreen 배포를 통해 Downtime 최소화
- 동시배포 가능
### 구성
- **Application** : 배포대상이 되는 application 프로젝트를 설정(Compute platform)
- **Deployment group** : 배포 대상에 대한 설정
- **Deployment type** : 배포를 어떻게 할 지(빌드 결과물만 변경할 것인지 혹은 Blue/Green 배포를 할 지)
- **Environment configuration** : Target이 될 배포 대상을 지정
- **Load balancer** : 배포 과정에서 LB의 트래픽을 막고, 배포된 이후 LB에서 트래픽이 흐르게 할 것인지 지정
#### How to apply?
- S3로부터 빌드 결과물을 EC2의 CodeDeploy Agent로 전달하여 Deploy로 정의된 appsec.yml 실행
- S3로 부터 빌드 결과물인 .zip 파일 풀어서 실행
- Appspec.yml에 zip파일의 shell 실행할 수 있도록 지정
- [AppSpec.yml 예제](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/reference-appspec-file-example.html#appspec-file-example-server) - 프로젝트 root 디렉토리
  
## CodePipeline
- Source - Build - Deploy 과정을 관리해주는 서비스
- 각 단계에 맞는 파이프라인 시각화 구성 가능
- 코드 변경에 따라 배포까지 가능
- Third party repository와 연동 가능
### 구성
- **Pipeline settings** : CodePipeline에 사용 될 Role과 아티팩트 저장소 등을 지정
- **Source Stage** : 다음 스테이지에 사용될 소스 지정
- **Build Stage** : 소스 스테이지에서 지정된 소스를 이용해 빌드 후 결과물 저장(Jenkins 사용 가능)
- **Deploy Stage** : 아티팩트를 선택하여 대상에 배포
#### How to apply?
- 앞서 만든 CodeBuild와 CodeDeploy를 연동하여 구축
- Source (github) -> CodeBuild(build Jar) -> CodeDeploy(Get S3 object & Deploy EC2)

## CodeStar
- IDE와 연동하여 소스코드 관리부터 배포까지 프로젝트의 형태로 관리해주는 서비스
- 통합 UI로 한 번에 여러 활동 관리 가능
- CD Tool Chain을 구성해 신속한 코드 배포 가능
- Owner, Contributor 및 End-User를 추가하여 안전한 협업 가능
- Dashboard를 사용해 전체 프로세스 상황 추적 가능

## 다양한 CI/CD 툴
### GitAction
- Github에서 제공하는 CI/CD를 위한 서비스
- Repository에 이벤트 발생 시 동ㅈ가하거나 주기적인 동작을 할 수 있도록 지정
- yml 기반의 definition
#### 구성
- [Workflow](https://docs.github.com/ko/actions/using-workflows/workflow-syntax-for-github-actions)
- event
- job
- step
- action

### Jenkins
- Github 혹은 BitBucket 등의 VCS 활용하는 CI/CD Tools
- 다양한 플러그인 지원으로 여러 솔루션과 연계 가능
- Self-hosted 형태
- 대시보드 및 테스트 수행 지원
- 특정 브랜치만 빌드할 수 있음
- shell로 빌드할 지, gradle 등 build 도구 사용할 지 선택 가능

### CircleCI
- Github 혹은 BitBucket 등의 VCS 활용하는 CI/CD Tools
- Jenkins와는 다르게 self-hosted server 필요 X
- yml 기반의 definition
- Docker workflow 지원
- 1달 6000분의 빌드 타임까지 사용하면 무료
- 빌드 속도 빠르게 해주는 캐시 기능

### TravisCI
- Github 혹은 BitBucket 등의 VCS 활용하는 CI/CD Tools
- Giyhub에서 제공
- yml 기반의 definition
- 30일 동안 무료(10000 credits), 이후 유료
- 오픈소스에서는 무료