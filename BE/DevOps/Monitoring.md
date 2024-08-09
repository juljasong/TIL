## 모니터링
- 지속적인 관찰을 통해 모니터링 대상의 상태나 가용성, 변화 등을 확인하고 예상하지 못한 상황과 오류에 대비하는 것
  - 클라우드 모니터링
  - 시스템 모니터링
  - 데이터베이스 모니터링
  - APM
  - UI 모니터링
  - CI 모니터링
  - 보안 모니터링

### 모니터링을 해야 하는 이유
1. 사전 분석을 통한 장애 방지
2. 다운 타임 최소화로 손실 방지
3. 생산성 및 성능 향상
4. 모니터링을 통해 IT 비용 예산 수립 가능
5. 데이터 기반의 의사결정 가능

### 모니터링 종류
#### 인프라 수준의 모니터링
- IaaS 수준에서 제공되는 서비스에 대한 모니터링
- 데이터베이스 혹은 네트워크 흐름을 모니터링하여 병목 지점 찾을 수 있음
#### 어플리케이션 수준의 모니터링
- 분산된 여러 클라우드 기반 앱을 한 곳에서 모니터링
- 인프라 수준 지표 뿐 아니라 비즈니스 트랜잭션 및 코드 레벨 까지 모니터링
#### 로그 수준의 모니터링
- 어플리케이션 혹은 액세스 로그 등의 요소를 수집하여 로그와 메트릭을 조합하여 특정 시점에 발생한 오류에 대해 인사이트를 찾아낼 수 있음

# AWS CloudWatch
- AWS 리소스와 AWS에서 실시간으로 실행 중인 애플리케이션을 모니터링하는 서비스
- 지표를 감시해 알림을 보내거나 Threshold값(임계값)을 위반한 경우 모니터링 중인 리소스를 자동으로 변경하는 경보 생성
- 대시보드, 알람, 이상탐지 등 지원

### AWS 서비스를 활용한 모니터링
- AWS에서 실행되는 자원 및 애플리케이션 모니터링
  - CloudWatch Agent를 설치하여 AWS 환경에서 실행되는 자원 또한 모니터링
  - 로그, 지표 및 이벤트 형태로 모니터링
  - 다른 서비스와 연계하여 자동화된 작업 및 트러블슈팅 등 다양한 형태로 활용 가능

### CloudWatch 지표
#### Metric
- 해당되는 지표 값(CPU Util)
#### Namespace
- 확인하려는 metric의 서비스
#### Dimension
- 지표의 그룹이 되는 기준
#### Statistics
- 평균, 최대, 최소, 합계 등

# 어플리케이션 성능 모니터링
- 어플리케이션 혹은 서비스가 동작하는데 호출한 동작에 대해 성능을 측정하기 위한 모니터링
- 비지니스 트랜잭션 혹은 코드 수준의 모니터링이기 때문에 인프라 수준 보다 더 세부적인 요소 살펴볼 수 있음
- 성능 측정 뿐만 아니라 병목 지점 등도 살펴볼 수 있음

### 종류
- **PINPOINT - 네이버 개발**
- SCOUTER
- PROMETHEUS - GRAFANA
- JAEGER

### PINPOINT 구조
#### TraceId 
- TransactionId와 SpanId, ParentId로 이루어진 키의 집합

#### TransactionId(TxId)
- 전체 서버군에 사용되는 메시지 Id
  
#### SpanId
- 메세지를 받았을 때 생성되는 작업의 Id

#### ParentId
- 호출한 SpanId

# 로그 모니터링
- 어플리케이션 혹은 서비스가 동작 중 발생한 오류나 여러 상황을 파일로 기록 -> **로그 파일**
- 로그 파일을 읽어 지표와 같이 확인함으로써 상황에 맞는 분석 할 수 있음
- 단일 노드인 경우 노드에 직접 접속해 tail 등의 명령어로 확인할 수 있으나, 클라우드 환경의 경우 분산된 환경의 여러 노드이기 때문에 로그 데이터를 수집하여 모니터링 하는 것 이 필수적인 요소

### 로그 모니터링 솔루션
- ElasticSearch + Logstash + Kibana (ELK)
- Grafana Loki
- Graylog

# SaaS 모니터링
- 서비스형 소프트웨어를 이용하여 별다른 관리 없이 Agent 설치와 수집만 가능하게 하면 유료로 할 수 있는 모니터링
- 지표 값을 수집만 하게 Agent를 설치하고 해당 Agent가 각 SaaS Endpoint로 데이터를 보냄으로써 모니터링이 가능
- 수집하는 모듈이나 UI까지 관리할 필요 없어 관리 하지 않고 사용한 만큼 금액을 지불하여 효율적인 모니터링 가능
- Infra, APM, DB, CI, UI, Log 등 다양한 모니터링 솔루션을 제공해주는 기업이 늘어나는 추세