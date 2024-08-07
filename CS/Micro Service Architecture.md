# MSA
## MSA Introduction
- 복잡한 어플리케이션을 **독립적으로 배포** 가능한
- 작은 서비스로 나누는 데 초점을 맞춘 소프트웨어 아키텍처 스타일
- 서비스의 **확장성(scalability)** 과 **유연성(flexibility)** 을 확보하기 위해 어플리케이션을 독립적인 소규모 서비스로 분리하는 소프트웨어 개발 접근 방식

### Background
- 2011 : SW 아키텍처 워크샵에서 공동의 관심사인 아키텍처 스타일을 설명하기 위해 처음 등장
- 2012 : Microservices를 이 아키텍처를 표현하는 가장 적절한 이름으로 결정
- 2014 : James Lewis와 Martin Fowler가 블로그에 기재, 관심을 받기 시작
- 마이크로 서비스의 정의 및 특징 정의 -> _MSA 이론의 표준처럼 인식_

### 주요 특징
- 각 서비스는 자체 프로세스에서 실행되고 **경량 메커니즘**으로 통신
  - REST
  - 메시지 큐
  - GRPC
- **비즈니스** 기능을 중심으로 분리
- 비즈니스 역량 중심의 조직 구성
- **자동화 된 배포 환경(인프라)** 을 통해 독립적으로 배포
- 각각의 서비스는 서로 다른 언어로 작성되고 서로 다른 데이터 저장 기술을 사용할 수 있음
- 중앙 집중식 관리가 거의 이루어지지 않음(**탈중앙화**)
- Monolithic vs MSA 
- **기술 선택의 유연성 (Tech Flexibility)** - 데이터 관점
  - 고객/상품 서비스 = 메타정보(원장) 관리, 구조화 된 정보, 대용량 처리
    - NoSQL - MongoDB, Amazon DynamoDB
  - 결제/주문 서비스 - 데이터의 정합성(신뢰성), 트랜잭션 보장, 백업 및 복구
    - RDB - MySQL, PostgreSQL, Oracle, MsSQL
  - 전시/광고/검색 - 빠른 조회
    - Elastic Search, In-Memory Databases - Redis

### 비즈니스 역량 기반의 조직 구성
- 모놀리식 
  - UI / 서버 / DB
- MSA
  - 검색 / 주문 / 결제
- Conway Rule - 기술의 구조는 조직의 구조를 따름
- MSA가 요구하는 조직
  - 교차 기능 팀
  - 자율성과 책임
  - 커뮤니케이션 및 협업

### Main Keywords
- 서비스 기반의 모듈화
- 비즈니스 역량 기반의 조직 구성
- 단일 책임 원칙 (SRP)
- 프로젝트가 아닌 프로덕트
- 탈중앙화 거버넌스
- 탈중앙화 데이터 관리
- 인프라 자동화

### DB의 분리
- 기능(서비스)은 분리 되었으나 데이터(DB)가 분리되지 않았다면?
  - 결제 처리 중 DB 부하로 인해 DB 서버에 장애가 생긴다면?
  - 대량의 상품 정보 갱신을 위한 배치 작업이 실행된다면?
  - Distributed Monolithic Application
  
### History
- **2001 : 애자일 선언**
  - 켄트 벡, 마틴 파울러
  - 소프트웨어 업계까 장기적인 계획이나 단계적 프로세스(폭포수 개발) 대신 점진적이고 반복적이며, 실용적인 실천법(스프린트)을 선호하게 됨
- **2006 : AWS EC2**
  - 아마존에서 IaaS 서비스인 EC2 발표, 최초 클라우드 사업 시작
- **2010 : NETFLIX - AWS 인프라 전환**
  - 넷플릭스는 비즈니스 확장으로 인한 스트리밍 데이터베이스의 스토리지 손실로 대규모 장애를 겪게 됨
  - 이 계기로 넷플릭스는 한 덩어리의 모놀리식 시스템에서 마이크로 서비스 기반의 시스템으로 전환, 인프라 환경을 클라우드로 이전
- **2012 : NETFLIX OSS**
  - 마이크로 서비스로 전환하는 과정 7년
  - 이 때의 문제점과 시행착오의 해결법을 오픈소스(Netflix OSS)로 공개
- **2013 : Docker 컨테이너 등장**
- **2013 : Spring Boot**
  - 마이크로 서비스를 쉽게 개발할 수 있는 Java 기반 Framework 발표 - Java 대통합 시대
- **2014 : Kubernetes**
  - 컨테이너 작업을 자동화하는 오픈소스 플랫폼. 구글 개발. CNCF에 프로젝트 기부

## Why, When, How MSA?

### MSA 구성요소와 아키텍처 패턴
- 어플리케이션의 복잡성 증가
- 개발 및 배포 주기 단축 필요
- 시스템의 확장성 및 복원력 요구 증가
- 클라우드 네이티브 아키텍처로의 전환
- 조직 효율성 및 DevOps 문화 요구
- 기술 다양성 및 선택권 확보

### Why MSA?
- **'장애를 극복하기 위한 생존의 문제'**
- 모놀리식
    - 결제가 안되는데(결제 DB) 로그인, 상품 검색, 주문 조차 되지 않는 상태
    - 결제 서비스가 정상화 될 떄 까지 모든 서비스 정지, 주문 불가
    - 큰 장애 복구 비용(전체 서비스의 복구)
    - 주문 불가, 사용자 이탈 및 매출 감소
  - 마이크로 서비스 어플리케이션
    - 결제는 안되지만 로그인, 상품 검색, 주문 까지는 정상 작동
    - 결제 전까지의 프로세스는 정상 작동
    - 장애 격리로 복구 비용 최소화
    - 고객경험/매출 손실의 최소화
- 비대해진 공통 코드.. 잦은 장애.. 밤샘 배포.. 위협받는 생존
- **Trend, Inovation** :: 가장 주목 받는 백엔드 기술 트랜드
  - MSA에는 분산 시스템, 클라우드 네이티브, 메세징 등 새로운 기술이 동반됨
  - **대규모 트래픽, 데이터**에 대응할 수 있는 아키텍처
- **Skill** :: 소프트웨어 엔지니어로서의 역량 향상
  - 현대의 분산 시스템 아키텍처의 중심 패턴
- DevOps와 Site Reliability Engineering(SRE) 문화 확산
  - 빌드/배포 자동화, 모니터링, 장애 대응 등의 역량이 중요해짐

### When MSA?
- 현재 운영 중인 서비스가 증가하는 **트래픽**으로 인해 서비스에 지장을 줄 정도의 장애가 발생하고 있는가?
- 그로 인한 서비스 **가용성**이 현저히 악화되고 있는가?
- 서비스의 복잡성이 현재의 모놀리식 구조에서 감당하기 힘들 정도로 높아져서 **서비스 확장성의 한계** 와 **개발 생산성의 저하**가 나타나는가?
- 어플리케이션 배포 시 그로 인한 서비스 전체의 **다운타임**이 서비스에 영향을 줄 만큼 지속적으로 증가하고 있는가?
- 서비스의 개발 보다 **장애대응, 배포**의 업무 비중이 증가하는가?

### How MSA?
- 고려사항
  - 서비스 성격과 개발/운영 조직과 환경이 MSA에 적합한지에 대한 검증과 철저한 준비 필요
- 스트랭글러 패턴
  - 기존 시스템을 한 번에 대체하는 대신, 시스템의 일부를 점진적으로 대체하면서 새로운 시스템으로 전환하는 방식
- 고려사항
  - **프록시 서버**를 통해 기존의 엔드 포인트로 요청되던 백엔드 요청을 가로채 새로운 서비스(MS)로 보내주는 작업 필요
- 꼭 필요할 때, 제대로된 조직 구성으로, 점직적으로!
  - [배달의 민족 마이크로 서비스 여행기](https://www.youtube.com/watch?v=BnS6343GTkY)

## MSA 도구 및 기술
- 구성요소
  - 서비스
  - API 게이트웨이(로드 밸런싱)
  - 서비스 등록/탐색
  - 서비스 별 데이터 베이스
  - 이벤트 중심 통신
  - 모니터링 및 로그
  - DevOps 및 CI/CD
  - 컨테이너화 및 오케스트레이션

### 서비스
- **상품 카탈로그, 사용자 인증, 주문 관리, 결제 처리, 배송** 등 시스템의 특정 비즈니스 기능을 의미 - 독립적으로 실행/배포될 수 있는 단위
- 서비스의 분리는 MSA로 가기 위한 첫번째 단계로 비즈니스 기반의 정확한 **서비스 경계**를 파악하는 것이 매우 중요 (**SRP, Data Ownership, Team**)
- **상품 카탈로그 서비스** - 상품 세부 정보, asset, 가격 등 제품 데이터 관리
- **인증 서비스** - 사용자 등록, 로그인 및 인증, 인가 처리
- **서비스 식별 및 도출**
  - 도입 방향성 검토
  - 현업 IT 담당자, 즉 제3자가 아닌 **시스템 오너**의 적극적인 참여 중요
  - **신규로 추가되거나 변경이 잦은 업무를 별도 서비스로 분리하고, 부하, 장애 등의 이유로 최우선으로 독립성을 확보해야 하는 서비스 식별**

### API 게이트웨이
- API 게이트웨이는 서비스로 전달되는 모든 API 요청의 관문(Gateway) 역할
- 모든 클라이언트 요청에 대한 **단일 진입점(Single Entry Point)** 제공
- 전체 시스템의 부하를 분산시키는 **로드 밸런서**의 역할, 동일한 요청에 대한 불필요한 반복 작업을 중일 수 있는 **캐싱**, 시스템 상 오고가는 요청과 응답에 대한 **모니터링** 역할 수행
- **인증 및 권한 부여** 같은 기능도 수행
- **시스템 내부의 아키텍처를 숨길 수 있는(encapsylate) 특성이 API 게이트웨이가 갖는 가장 큰 장점**
- **로드 밸런싱**
  - 클라이언트 요청에 대해 여러 서비스 인스턴스로 고르게 분산처리 담당
  - 서비스 성능과 유지에 필요한 가장 중요한 구성 요소 중 하나
    - **서비스의 내결함성(Fault tolerance), 고가용성(High availability)** 을 결정

### 서비스 등록/탐색
- 서비스 탐색
  - 끊임없이 변화하는 환경에서 마이크로 서비스가 서로의 서비스를 동적으로 찾을 수 있도록 해줌.
  - 이를 통해 서비스는 다른 서비스의 네트워크 위치를 검색하고 서로 연결할 수 있음
  - 각 서비스들은 다른 서비스의 연결정보를 직접 가지지 않고, Service Discovery에 위임
  - Netflix eureka, Apache Zookeeper, ..

### 서비스 별 데이터 베이스
- 각 마이크로 서비스는 특정 기능과 관련된 데이터를 관리하는 자체 데이터 베이스를 구성하는 것이 원칙.
- 이를 통해 데이터 격리를 보장하고 각 서비스는 가장 적합한 데이터 베이스 기술을 선택하여 구성

### 이벤트 중심 통신 Event-Driven communication
- 마이크로 서비스는 이벤트 또는 메시지 브로커를 통해 비동기적으로 통신하는 것을 권장
- 이를 통해 서비스 간 **느슨한 결합(loosly coupled)** 과 **유연성(felxbility)** 확보
- 주문 생성 시 주문서비스는 주문 데이터 **발행**, 결제 서비스는 주문 데이터 **구독**
- 결제 서비스는 결제 처리 후 결제 완료 이벤트 **발행**, 필요한 서비스(고객, 주문, 배송, 회계, 서비스 등) 결제 데이터 **구독** 후 비즈니스 로직 수행
- **즉, 느슨한 결합이 가능 -> 서비스 간 상호 의존성이 배제**
- amazon SQS, APACHE kafka, RabbitMQ, ..

### Logging & Monitoring
- 모니터링 및 로깅 도구는 마이크로 서비스의 성능 추적 및 가용성 확보, 유지하는 데 매우 중요한 구성 요소
- **분리된 서비스들 로그의 통합 및 지표들의 정의**가 중요
- 사용자들의 활동에 대한 분석과 활용, 시스템 동작에 대한 인사이트 제공
- 서비스 주요 메트릭 모니터링 - Prometheus와 Grafana
  - '결제 처리 서비스'의 응답 시간을 모니터링하고 성능 문제 진단
- 중앙집중식 로깅 - ELK Stack(Elasticsearch, Logstash, Kibana)
  - 시간대 별 주문량 추이 등의 패턴 분석, 빅 데이터 활용

### 지속적인 통합 및 배포 - CI/CD
- 마이크로 서비스 아키텍처는 **빈번한 변경**과 그에 따른 **즉각적인 반영(배포)** 를 요구함
- CI/CD 파이프라인은 마이크로서비스의 **배포와 테스트를 자동화** 하여 신속한 개발과 지속적인 배포 보장
- Jenkins 또는 GibLab CI/CD 파이프라인은 변경 사항이 코드 리포지토리(Git hub)에 Push될 때 마다 서비스 빌드, 테스트 및 배포를 자동화 할 수 있음

### 컨테이너화, 오케스트레이션
- 확장 가능하고 탄력적인 방식으로 마이크로 서비스를 **패키징**, 배포 및 **관리**하기 위해 Docker와 같은 컨테이너와 Kubernetes와 같은 오케스트레이션 도구가 사용됨
- **Kubernetes**를 사용하게 되면 **클러스터의 여러 노드**에 걸쳐 상품 카탈로그 서비스의 여러 인스턴스를 자동으로 배포, 관리하여 고가용성 보장

## MSA - 아키텍처 패턴
- **Pattern?** 
  - 문제 영역에 대한 검증된 유용한 해법
- MSA 도입하려면 그 아키텍처의 복잡성으로 인해 여러 가지 문제 영역에 부딪히게 됨
  - 분리된 서비스, 분리된 DB, 서로 다른 언어와 기술, 다양한 르포토콜 기반의 네트워크 통신
  - 즉, 이런 분산환경에서의 서비스 구성 시 발생 가능한 여러 문제 영역들이 존재하게 됨
  - ex) 시스템 복잡도 증가, 장애 전파, 성능 이슈, 데이터 정합성 등

### MSA 외부 아키텍처
- 인프라 구성 패턴
  - 마이크로 서비스를 지탱하는 하부 구조 인프라를 구축하는 데 필요한 구성요소 
  - **퍼블릭 클라우드**
    - **IaaS**
      - Infra as a Service
      - 가상 머신, 스토리지, 네트워크 같은 인프라를 필요한 만큼 적시에 제공하는 서비스
    - **PaaS**
      - Platform as a Service
        - 복잡함 없이 애플리케이션을 곧바로 개발, 실행, 관리할 수 있는 플랫폼 환경을 서비스 형태로 제공
        - OS + 프로그램 언어 런타임 + 웹 서버 + 애플리케이션 서버 + 추가 구성요소 조합
    - **CaaS**
      - Container as a Service
      - 컨테이너 기반 가상화를 사용해 컨테이너를 업로드, 구성, 실행, 확장할 수 있는 서비스
      - 애플리케이션을 바로 구동할 수 있는 환경을 제공한다는 점에서 PaaS와 유사하나 다른 환경에서도 이식 가능한 컨테이너 기반 가상화를 제공한다는 점이 다름
  - 베어 메탈 + 프라이빗 클라우드 - 보유한 베어 메탈 장비에 프라이빗 클라우드를 직접 구축 (Vmware 등)
- 플랫폼 패턴
- 애플리케이션 관계 패턴

### MSA 내부 아키텍처
- 마이크

### 주요 아키텍처 패턴
- **Service Registry / Discovery 패턴**
  - **Service Registry** 패턴
    - 마이크로 서비스의 명칭과 유동적인 IP 정보를 매핑해서 보관 및 제공하는 기능을 수행
  - **Service Discovery** 패턴
    - 서비스 클라이언트가 다른 서비스를 호출할 때 서비스의 접속 주소를 알아낼 수 잇는 기능 수행
    - **Client-Side Discovery** -> 라이브러리
      - client가 service registry에서 서비스의 위치를 찾아 호출
      - client에서 라우팅 및 로드 밸런싱 수행, 대표적으로 Spring cloud Netflix(Eureka, Zookeper, Ribbon)
    - **Server-Side Discovery** -> 비용 필요
      - 호출 되는 서비스 앞에 일종의 Proxy(로드 밸런서)를 넣는 방식
      - client가 로드 밸런서 호출 -> 로드밸런서가 service registry로 부터 등록된 서비스의 위치 받아 이를 기반으로 라우팅
      - AWS ELB, GCP Cloud Load balancing 
- **API Gateway 패턴**
  - 클라이언트와 서비스 사이에 프록시 처럼 위치하여, 클라이언트로 부터 유입되는 API 요청을 받아 적절한 서비스로 라우팅
  - 클라이언트가 접근 가능한 단일 진입점(Single Entry Point)을 제공해 여러 유용한 기능을 제공
  - **제공 기능**
    - **요청 라우팅 - Routing**
      - API 게이트웨이는 요청을 검사하고, 대상 서비스를 식별하고, 요청을 해당 서비스로 전달
      - 라우팅은 서비스 레지스트리 서비스와 연계하여 제공
    - **인증 및 승인**
      - 권한 서비스와 연계한 인증/인가 기능 제공
      - 사용자 자격 증명 확인, 액세스 제어 시행, 승인된 클라이언트만 특정 서비스에 액세스할 수 있도록 보장하는 작업 포함
    - **프로토콜 변환**
      - 클라이언트와의 통신은 REST API, 내부 서비스 연계는 gRPC 사용
    - **로드 밸런싱**
      - 여러 인스턴스에 요청을 균등하게 분산 처리
      - 고가용성과 최적의 리소스 활용 보장
    - **요청 변환**
      - API 게이트웨이는 요청과 응답을 수정, 변환해 클라이넌트와 서비스 간 데이터 형식이나 구조 조정
      - 버전 관리, 데이터 정규화 또는 데이터 변환에 유용
    - **응답 캐싱**
      - 게이트웨이 수준의 캐싱 기능
      -  서비스에 대한 로드 줄이고 자주 액세스하는 데이터에 대한 응답시간 향상
    - **로깅 및 모니터링**
      -  중앙집중식 로깅 및 모니터링 제공
      -  API 사용량, 성능 및 오류 추적, 모니터링 서비스와 연계한 장애 격리 기능
  - **도입 필요성**
    - **보안**
      - 인증/인가 프로세스를 통합하고 서비스가 인터넷에 직접 노출되지 않도록 보호
      - DDoS 및 인젝션 공격과 같은 일반적인 공격에 대한 방어 효과
    - **트래픽 관리**
      - API 트래픽을 효율적으로 고나리, 요청을 적절한 서비스로 보내고 로드 밸런싱 구현
      - 고가용성과 확장성 확보
    - **API Composition**
      - Client와 서버 간 여러 번의 네트워크 왕복이 발생할 수 있어 처리 시간이 늘어날 수 있음
      - 중간 수준에서 처리되는 가공(집계, 머지, 필터링 등)를 통해 앱의 성능과 사용자 환경 향상
    - **그 외 다양한 문제 해결**
      - 유효성 검사, 액세스 제어, 응답 전문 형식 지정 등 서비스 전반에 걸친 공통 문제(횡단 관심사)를 처리하여 내부 마이크로서비스가 단순화(비즈니스 로직 집중)
  - 관련 기술들
    - SaaS : AWS API Gatewaty, GCP Cloud EndPoint, Nginx(Web-SErver)
    - Spring Cloud Gateway, Netflix Zuul
- **외부 구성 저장소 패턴**
  - 마이크로 서비스들이 필요로 하는 애플리케이션 구성 정보를 일관되게 관리하기 위한 방법
  - **구성정보는 외부 저장소에서 일괄적으로 관리**, 서비스 코드와 분리
  - 환경 설정의 변경으로 인한 서비스 변경 및 배포 의존성 없애기 위한 패턴
  - [The Twelve-Factor App]
    - Cloud Native Application 개발을 위한 12가지 규칙
    - 3rd Config : 외부 구성 저장소 패턴의 바탕
    - 클라우드에서 운영되는 애플리케이션은 특정한 배포 환경에 종속된 정보를 코드에 두면 안된다는 원칙
    - 설정 정보는 외부 저장소에 관리, 각 서비스가 변경과 배포 정차 없이 일괄적으로 외부에서 설정 정보를 주입받을 수 있도록 처리
- **중앙화 로그 집계 패턴**
  - 각 서비스가 생성하는 로그를 수집 및 집계하여 중앙화된 시스템에서 제공하는 것이 중앙화된 로그 집계 패턴
  - 필요 이유
    - 마이크로 서비스가 사용량에 따라 탄력적 인스턴스가 생성/삭제 될 수 있고, 여러 서비스에서 생성된 로그를 하나하나 직접 확인하는 것이 불가능
    - 분산 환경에서의 서비스 운영과 모니터링 위해서 로그의 통합 중요
  - **ELK Stack**
    - Elastic Search
      - 분산형 검색 및 분석 엔진
      - 정형, 비정형, 위치 정보, 매트릭 등 원하는 방법으로 검색을 수행 결합 가능
    - Logstash
      - 로그 집합기
      - 데이터 처리 파이프라인. 다양한 소스에서 동시에 데이터를 수집/변환, 특정 보관소로 데이터 전송
    - Kibana
      - 데이터 시각화
      - 차트, 위치 데이터, 시계열 분석 그래프 관계 등 지원
- **CQRS (Command-Query Responsibility Segregation) 패턴**
  - **명령(Command, CUD - RDB)** 과 **조회(Query, R - No-SQL)** 책임의 분리
  - 시스템의 읽기 및 쓰기 작업을 명령 모델(업데이트용)과 쿼리 모델(읽기용)의 두 가지 모델로 분리하는 패턴
  - DB 분리 기반
    - 명령/조회를 위한 db를 분리하고 별도의 message broker를 통해 data 동기화 처리
    - 결과적 일관성 지원
    - 동일 db사용에 따른 성능 문제 해결 가능
    - data 동기화 처리를 위한 message broker 가용성과 신뢰성 보장되어야 함
    - 물리적으로 2개의 db 사용해 읽기 및 쓰기 db 분리하는 것이 이상적
- 마이크로 서비스 통신 패턴
  - 서비스 간 효율적인 커뮤니케이션 위한 통신 패턴
    - **동기식** vs **비동기식**
    - 선택은 애플리케이션의 특정 요구 사항과 **지연시간(latency)** 과 **확장성(scalability)** 간의 원하는 절충점에 따라 달라짐
  - 동기
    - 요청/응답
      - Blocking - 블로킹
      - titly coupled - 서비스가 서로 강하게 결합되는 방식
      - request, response
      - 결제서비스, 인증 서비스, 재고 관리 서비스 등
  - 비동기
    - 비동기 요청/응답 
      - Non blocking
      - loosly coupled - 서비스가 느슨하게 결합되는 방식
      - message, publish, subscribe
  - 관련 기술
    - HTTP
      - 가장 대중적인 서버간 요청-응답 통신 방식. 웹에서 사용할 수 있는 HTTP/HTTPS 프로토콜 사용
      - 텍스크 기반(XML/JSON)의 전송테이터 사용, 단방향 통신
    - gRPC
      - 구글이 최초로 개발한 오픈 소스 원격 프로시저 호출 시스템
      - HTTP/2 기반의 바이너리 프로토콜 사용, 데이타 직렬화에 프로토콜 버퍼(protobuf)를 사용
      - 단일연결에서 여러 요청과 응답 처리가능(Multi-Flexing), 양방향 통신
    - GraphQL
      - 페이스북에서 만든 HTTP기반 클라이언트 쿼리언어. End-Point가 하나만 존재
      - 웹 클라이언트가 데이터를 서버로 부터 효율적으로 가져오는 것이 목적

### Spring Cloud
- Spring 팀에서 만든 일종의 포괄적 프로젝트
- **분산 시스템 개발에 필요한 곹옹의 패턴**들을 모아 사용하기 쉬운 스프링 라이브러리 형태로 구현해서 제공하는 라이브러리 집합체
- 클라우드 기술 또는 솔루션 의미X
- 클라우드 기반 어플리케이션 개발에 필요한 **12요소 어플리케이션 원칙** 을 준수하며 **클라우드에 배포될 시스템을 개발할 때 필수적인 기능** 제공 기술 모음
- 배포 환경과 무관하게 사용 가능
- 개발된 소스는 Cloud Foundry, AWS, Heroku 등과 같은 다양한 클라우드 밴더로 배포, 이관 가능
- Spring Cloud를 사용하면 개발자들은 분산, 장애대응, 자체 복구 등의 기능은 Spring cloud에 맡기고, 스프링 부트를 바탕으로 **비즈니스 기능을 개발하는 데만 집중** 할 수 있게 해줌
- **Release Train** : 여러 하위 모듈(프로젝트)를 묶어 배포하는 프로젝트에서 상위 모듈의 버전을 관리하는 것
- 기능
  1. 분산 환경 설정 - Spring Cloud Bus, ___Spring Cloud Config___
  2. 서비스 등록 및 발견 - Zookeeper, Consul, ___(Netflix) Eureka___
  3. 분산 메세징 - Streams
  4. 라우팅 - ___Spring Cloud Gateway___
  5. 부하 분산 - (Netflix Ribbon) LoadBalancer
  6. 서비스 대 서비스 호출 - ___(Netflix Feign) OpenFeign___
  7. 보안 - Security
  8. 전역 잠금, 주도권 선발 및 클러스터 상태 - Cluster
  9.  클라우드 지원 - Connectors, AWS, Cloud Foundary
  10. 빅데이터 지원 - Spring Data Flow
  11. 분산 추적 - ___Sleuth___
  12. 서킷 브레이커 - ___(Netflix Hysterix) Circuit Breaker___
- MSA에서 중요한 요소 중 하나는 자동 확장(Auto Scale-out)
  - 특정 서비스에 트래픽이나 부하가 증가할 경우 인스턴스의 수를 늘림(**Scale-Out**)
  - 트래픽과 부하가 감소할 경우 불필요한 인스턴스를 서비스에서 제거(**Scale-In**)
  - 클라우드 기반의 인프라 환경에서는 **Auto Scaling** 필수
  - => __변경이 잦은 서비스 간 로드밸런싱과 장애 대응 필요__

### Spring Cloud - Config
- 분산 환경 설정 - 환경 정보의 외부화
- Config Server
  - 마이크로 서비스의 환경설정정보-특히 서비스, 비즈니스 로직과 연관성이 있는 정보들-을 어플리케이션과 분리해 외부의 서버를 통해 관리해주는 기술
  - 환경설정 파라미터들을 외부화하고, 개발, 테스트 운영 등의 여러 개의 프로파일로 운영되는 마이크로 서비스의 설정들을 중앙집중적(Git, File Server, DB 등)으로 관리
  - 마이크로서비스의 설정(환경 변수 값, Spring Cloud 설정 등)이 변경되었을 때 서버 재시작 없이 동적으로 적용
  - 동작
    - Config Server 구동 후 Git 등의 저장소로 환경설정 정보 얻어옴
    - 마이크로 서비스는 Config Server에 접근하여 환경 설정 정보 읽어옴
    - 갱신을 위한 api 호출 (/refresh) -> 변경된 설정정보 적용 (Actuator)
  - Actuator 라이브러리는 필수는 아니지만, Config Server 설정 후 EndPoint를 통한 Config SErver의 구설정보나 상태를 확인하기 위해서는 필요함
  - 환경설정의 변경상태를 재기동 없이 읽어오기 위해서는 Actuator가 제공하는 Refresh Endpoint(/actuator/refresh)를 사용해야 함. **Config Cloud Bus를 사용하여 대체 가능**
  - 개발, 테스트, 운영 등의 여러 개의 프로파일로 운영되는 마이크로서비스의 여러 인스턴스들의 환경 설정 속성을 외부화하고 중앙 집중.......ㄴㅇㄹ

### Netflix Cloud Netflix - Eureka
- 인스턴스의 상태를 동적으로 관리하는 서버 (서비스 디스커버리 서버)
- 주요기능
  - 새로운 인스턴스는 시작할 때 Eureka 서버에 IP, 호스트 주소, 포트 정보 등을 스스로 전송
  - Eureka 서버는 받은 정보를 가지고 일정한 간격으로 상태를 체크하며 해당 인스턴스를 관리
  - 인스턴스가 새로 실행될 때 마다 자신의 정보를 서버에 동적으로 등록
- 동작
  - Eureka 사용할 모든 Server에 Eureka Client 탑재
    1. 서버 가동 시 자신의 정보(ip, port, 서비스명)을 Eureka Server에 등록(종료 시 삭제)
    2. Eureka Client는 주기적으로 Service 별 IP 목록을 Fetch 후 보관
    3. API 호출 시 서비스 이름(Service A)를 사용하여 해당 서버 목록 획득 후 호출
  - Eureka 사용할 모든 Server에 Eureka Client 탑재
    1. 서비스 기동 시 등록, 종료 시 삭제
    2. 서비스 인스턴스 추가 및 삭제(Scale-in, Scale-out) 시 자동 등록/삭제
    3. 서비스 수평 확장 시 IP 정보에 대해 신경 쓸 필요 없음


### Netflix Cloud Netflix - Ribbon
- 클라이언트 사이드 로드 밸런서
- 소프트웨어 방식의 Client-Side Load Balancer
- 요청을 보내는 클라이언트 측에서 서버의 부하 분산 수행(다양한 알고리즘)
- 로드밸런서의 특징 상 단독이 아닌 API Gateway, Eureka 와 같이 사용하는 경우가 많음
- Spring Cloud Loadbalancer로 이름 바뀜
- 모놀리식 시스템엥서는 부하 분산을 위해 L4/L7 스위치 같은 하드웨어 장비를 앞단에 두고 트래픽을 여러 서버로 분산시키는 방식이 일반적(ELB의 경우에도 논리적인 스위치 개념)
- **로드 밸런서 장비(하드웨어)에 장애가 발생하면 전체 서비스에 장애가 생기는 리스크가 높음**
- **동적으로 서버가 추가, 삭제되는 클라우드 환경에서 하드웨어 장비로 대응하는 것도 한계**
- 클라이언트 : PC나 모바일 단말기가 아닌 MSA에서 다른 서비스를 호출하는 클라이언트 서비스
- 클라이언트 서비스는 **부하 분산 알고리즘**에 따라 서버 목록 중 하나를 선택하여 호출
- API Gateway에 임베드 되어 사용하거나, Eureka(서비스 탐색)와 함께 사용
- 주요기능
  - **Rule** : 요청을 보낼 서버 선택하는 알고리즘
    - **Simple Round Robin** : 서비스를 순서대로 선택(Default)
    - AvailabiltyFilteringRue : 가용성 높은 서비스 선택(내장된 서킷 브레이커 모듈 이용)
    - Weighted Response Time : 서버의 응답 시간에 따라 응답 시간 빠른 서비스 선택
    - **Availabilty filtering** : 에러가 많은 서버 제외(3회 연속 호출 실패 시 30초 동안 목록에서 제외)
    - 등등 ㅋ
  - **Ping** : 서버 list가 살아있는지 체크 로직
    - IPing 인터페이스 구현한 클래스 사용. 
    - 주기적으로 서버들의 상태를 체크, 서버가 죽었거나 비활성화.......2342
  - **ServerList** : 로드밸런싱 대상 서버 목록 조회
    - configuration을 통해 static하게
    - Eureka등을 기반으로 dynamic하게 설정 가능

### Netflix Cloud Netflix - Hystrix
- MSA도 특정 서비스에 과부하가 걸리거나 장애가 생기면 전체 서비스에 장애를 전파하는 경우 발생
- 서비스가 분리되었다고 장애가 전파되지 않는 게 아님
- MSA 환경에서 장애 관리(격리)가 안 될 경우 모놀리식 보다 처리에 더 어려움
- 서킷 브레이커(회로 차단기)
  - 특정 서비스에 문제가 생기더라도 전체적으로 장애가 확산되지 않도록 차단해주는 기능
- 주요 기능
  - **API 호출 통계**를 기반으로 상대 서비스에서 이상을 감지하면 즉시 통신 중단
  - 문제가 발생한 서비스를 **격리** 처리(**isolation**) - 분산 시스템의 복잡한 연쇄 실패 방지
  - **빠르게 실패하고 빠르게 복구**
  - **Fallback**과 **Gracefully Degrade**
  - 문제가 해결될 때 까지 별도의 스레드에서 밀린 작업을 수행하거나 호출 수를 제한
  - **모니터링과 알람기능** 제공(with Turbine)
- Circuit Breaker State Diagram
  - CLOSED : 정상
  - OPEN : 에러발생
  - HALF-OPEN : 제한된 서비스만 정상이라고 판단, 복구 중인 서비스에 많은 트래픽이 몰리는 경우를 방지하기 위해 제한된 요청만 허용하기 위한 상태

## MSA 주요 적용 사례
### Netflix

### Amazon
- SOA (Service Oriented Architecture) : 서비스 중심의 아키텍처
- 제프 베조스(Jeff Bazo's, 아마존 CEO)
  - 기능 및 데이터를 Service Interface를 통해서만 노출해야 함
  - 다른 팀의 기능/데이터 사용하려면 Network 통한 Service Interface 호출해야 함
  - Service Interface 기술은 상관 X (HTTP, .. )

### Uber
- 차량 공유(Car Sharing) 서비스
- 모놀리식 -> 출시 얼마 지나지 않아 바로 한계에 부딪힘
- 서비스 글로벌 확장을 위한 시스템 변경, 배포의 어려움
- 하나의 새로운 기능 적용을 위해 다른 기능 모두를 재구성하고 재배포 해야 함
  - 과금, 드라이버 서비스의 경우 글로벌 확장 시의 잦은 변경 발생(서로 다른 정책, 법률, 규제 등)

## MSA 장단점
### 장점
- 모놀로식 아키텍처 한계 : 비즈니스 성장으로 서비스 규모가 커질 때 한계가 나타남
  - 어플리케이션 복장성 증가로 인한 개발 속도 저하
  - -> 배포 시간 증가
  - -> 오류 발생의 증가(Side Effect, 버그 재생산) - SRP 부재
  - 부분적 확장(Scale-Out)의 어려움, 장애의 반복
- 모놀로식
  - 장점 : **단순성**
    - 단일 배포 단위
    - 통합 데이터 관리
  - 단점
    - 제한된 확장성, 전체 배포
    - 복잡성(단일코드의 복잡성)
    - 개발 속도 저하, 배포 시간
- MSA 
  - 장점 : **확장성/유연성**
    - 독립적인(부분적인) 확장(비용)
    - 더 빠른 개발
  - 단점
    - 분산된 복잡성
    - 운영 오버헤드 증가
    - 데이터 관리 과제
- MicroService Pros
- MicroService Cons
- 

### 단점


# 실전 면접
**Q1: 마이크로 서비스란 무엇이며 모놀리식 아키텍처와 어떻게 다른가요?**
A : 독립적으로 실행, 배포 가능하고, 데이터 분리도.. 정리해보기

**Q2: API 게이트웨이란 무엇이며, 마이크로서비스에서 어떤 역할을 하나요?**
A : 클라이언트 요청에 대한 단일 진입점. 마이크로서비스의 내부 구조를 숨길 수 있게 해줌.

**Q3: 마이크로 서비스의 맥락에서 서비스 검색의 개념과 그 중요성에 대해 설명하시오.**
A : Scale-in/out 관련 찾아보기

**Q4: Spring Cloud의 대표적인 마이크로서비스 관련 기술 3가지 얘기하고 각각의 특징과 역할 설명하시오.**
A : 

**Q5: 마이크로서비스 아키텍처에서 데이터 일관성을 어떻게 보장(관리)할 수 있나?**
A : 구체적인 해법 보다 접근방법에 대해.. 쉽게 보장되지 않는다.... Event-Driven Saga Pattern

**Q6: 모놀로식 시스템에서 마이크로 서비스로 전환해야 하는지 비즈니스 관점에서 설명해 보시오.**
A : 아마존~~ 서비스의 가치. 지속적인 변경에 따른 빠른 배포. 장애의 격리를 통한 빠른 복구 가능(서비스 분리)