## 성능 테스트
- 특정 워크로드에서 어플리케이션의 안정성과 속도, 확장성 및 반응성이 어떻게 유지되는지 판별하는 방법
- 소프트웨어의 품질을 보장하는 방법이지만 대부분 나중에 고려할 요소로 남겨둠
- **성공적인 성능 테스트는 데이터베이스, 네트워크, 소프트ㅜ에어, 하드웨어 등과 관련 될 수 있는 대부분의 성능 문제를 예측해야 함**

### 필요한 이유
- 어플리케이션 혹은 서비스가 충분히 성능 요건을 충족하는지
- 병목 지점이 없는지
- 최대 트래픽에서 안정성에 이상 없는지
- 오랫동안 같은 동작 할 수 있는지

### 순서
1. 테스트 환경 및 테스트 툴 파악
2. 제약 사항 파악
3. 시나리오와 테스트 데이터 정의, 통계 수집 방법 정의
4. 테스트 환경 설정 및 설계
5. 테스트 설계에 대한 구현
6. 테스트 실행
7. 테스트 데이터 분석 및 리포팅, 재 테스트

### 종류
#### Endurance Test (내구성 테스트)
- 긴 시간 동안 부하를 가하여 시스템의 안정성을 점검하는 테스트
- 8시간 이상

#### Spike Test (스파이크 테스트)
- 순간적으로 사용자 수를 증가시키는 테스트

#### Stress Test (스트레스 테스트)
- 정상보다 더 많은 부하를 주는 테스트
- 피크 로드(최대 처리량)의 2배

#### Load Test (부하 테스트)
- 일정 시간 동안 부하를 가하여 처리할 수 있는 최대 TPS와 응답시간을 구하는 테스트
- 1시간 정도

#### Volume Test
#### Scalability Test

## 성능 테스트 지표
- 성능 테스트에 대해 설정 하고, 이를 수행한 다음 서비스에 대해 성능을 객관적인 데이터로 보는데 사용
- 이 데이터들을 활용해 서비스의 임계점이나 병목지점 찾을 수 있음
- 실제 서비스를 운영하는 호나경과는 다르지만 예측할 수 있는 데이터로 활용됨

### 성능 테스트 설정 관련 지표 및 개념
- **vUser** : 가상 사용자 수
  - 에이전트 수 * 프로세스 수 * 스레드 수
- **Active user** : 메뉴나 링크를 누르고 결과가 나오기를 기다리는 등 실제로 서버에 부하를 주고 잇는 사용자
- **concurrent user** : 웹 페이지를 띄워놓은 사용자처럼, 언제든지 부하를 줄 수 있는 사용자
  - 동시접속자 수
- **Ramp-up** : 점진적으로 사용자를 늘리거나 트래픽을 늘릴 때 사용
- **TPS** : 초당 트랜잭션 개수
  - (Active User) / (Avg Resp Time) = (Concurrent User) / (Req Interval)
- **Response Time(Latency)** : 사용자가 서버에 요청을 한 시간에서 부터, 응답을 받을 때 까지의 모든 시간을 포함
  - TPS * (Avg Resp Time) = (Concurrent User) * (Avg Resp Time) / [(Avg Resp Time) + (Avg Think Time)]
- **Think Time** : 사용자가 요청에 대해 응답 받은 후에, 웹 페이지를 보거나 화면을 보는 등의 작업을 하는 시간
- **Request Interval Time** : 요청과 다음 요청 사이의 간격

## JMeter
- Apache 재단에서 오픈소스로 만든 Java 기반의 성능 테스트 툴
- 생성된 .jmx 파일을 이용해 cli로 실행도 가능
- 다양한 프로토콜 지원
  - HTTP, HTTPS, SOAP, REST, FTP, JDBC, LDAP 등

### 주요 개념
#### Thread Group
- 몇 개의 쓰레드가 동시에 요청을 보내는 지
#### Sampler
- 어떤 유저가 해야하는 액션
#### Listener
- 응답을 받았을 때 어떤 동작을 취하는 지(검증, 리포트, 그래프 그리기 등)
#### Configuration
- Sampler 또는 Listener가 사용할 설정 값(쿠키, JDBC 커넥션 갯수 등)
#### Assertion
- 응답 결과의 성공 여부를 판단하는 조건(응답 코드, 본문 내용 등)

### 설치
- [JMeter](https://jmeter.apache.org/download_jmeter.cgi)
- [JMeter Plugin](https://jmeter-plugins.org/install/Install/)

## nGrinder
- 네이버에서 성능 측정 목적으로 jython(JVM 위에서 동작하는 파이썬)으로 개발된 오픈소스 프로젝트
- 바닥부터 개발한 것이 아니라 The Grinder라는 오픈소스 기반하여 개발
- Custom library를 활용한 Test 가능
- Web UI 제공
- 부하를 주는 Agent와 부하를 받는 Target 서버에 대한 모니터링
- 동시에 여러 성능 테스트 가능

### 아키텍처
#### Controller
- 성능 측정을 위한 웹 인터페이스 제공
- 테스트 프로세스 조정
- 테스트 통계를 수집하고 표시
- 스크립트 수정 기능 제공
#### Agent
- 에이전트 모드에서 실행할 때 대상 시스템에 부하를 주는 프로세스 및 스레드 실행
- 모니터 모드에서 실행 시 대상 시스템 성능(CPU/메모리) 모니터링
#### Monitor
- 부하의 대상이 되는 서버에 리소스를 모니터링하기 위한 모듈

### 기동
```shell
$ docker run -d --name controller -p 80:80 -p 16001:16001 -p 12000-12009:12000-12009 ngrinder/controller # controller 기동

$ docker run -d --name agent --link controller:controller ngrinder/agent # agent 기동

$ docker logs -f CONTAINER_ID
```
- ```-p 80:80``` : 웹 포트, console 들어가기 위한 포트
- ```-p 16001:16001``` : 에이전트가 컨트롤러에 접속하기 위한 포트
- ```-p 12000-12009:12000-12009``` : 실제 테스트가 수행되는 포트

- web에서 localhost 접속 (id : admin / password : admin)

