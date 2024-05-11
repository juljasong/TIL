# MSA
## MSA Introduction
- 복잡한 어플리케이션을 **독립적으로 배포** 가능한
- 작은 서비스로 나누는 데 초점을 맞춘 소프트웨어 아키텍처 스타일
- 서비스의 **확장성(scalability)** 과 **유연성(flexibility)** 을 확보하기 위해 어플리케이션을 독립적인 소규모 서비스로 분리하는 소프트웨어 개발 접근 방식

### Background
- 2011 : SW 아키텍처 워크샵에서 공동의 관심사인 아키텍처 스타일을 설명하기 위해 처음 등장
- 2012 : Microservices를 이 아키텍처를 표현하는 가장 적절한 이름으로 결정
- 2014 : James Lewis와 Martin Fowler가 블로그에 기재, 관심을 받기 시작
- 마이크로 서비스의 정의 및 특징 정의 -> MSA 이론의 표준처럼 인식_

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

## MSA 주요 적용 사례

## MSA 장단점


# Git 브랜치 전략
- **여러 개발자**가 **한 개의 저장소**를 사용하는 환경
- [대표 전략](https://hyeon9mak.github.io/git-branch-strategy/)
  - GitHub flow
  - Git flow
  - GitLab flow
  - Trunk Based
  - [유튜브~](https://www.youtube.com/watch?v=wtsr5keXUyE)

## GitHub flow
- master 브랜치 중심
- 기능 개발 버그 수정 등의 작업용 브랜치를 구분하지 않는 단순한 구조
- 수시 배포가 일어나는 프로젝트에 유용

### 브랜치 생명주기
1. 브랜치 생성
     - master로 부터 기능 추가, 버그 수정 작업을 위한 새로운 브랜치 생성
     - commit 메시지와 브랜치 이름은 정확하고 간결하게 작성
     - 기능 추가, 버그 수정 작업의 모든 브랜치는 master에서 나옴
2. 기능 개발, 버그 수정
     - 작업하며 기능 별로 commit
     - commit 메시지는 정확하고 간결하게 작성
     - commit은 서버의 동일한 브랜치에 push
3. Pull Request(PR) 생성
4. 리뷰와 논의
5. 공개 및 테스트
     - GitHub에서는 master에 합치기 전 브랜치에서 코드를 공개 및 테스트 할 수 있음
     - PR 상의와 테스트가 끝난 경우 (테스트 버전으로) 공개 가능
     - 오류가 발생할 경우 원래의 master 브랜치를 다시 배포하여 roll back
6. Merge

## Git flow
- 현재 많은 기업에서 표준으로 사용
- 크게 5개의 브랜치를 운영
  - 메인 : master, develop
  - 보조 : feature, release, hotfix
- 배포 주기가 길고 팀의 여력이 있는 경우 적합

### 메인 브랜치 (계속 유지)
- **master** : 제품으로 배포할 수 있는 브랜치
- **develop** : 개발자들이 개발 하는 브랜치

### 보조 브랜치 (사용 마치면 삭제)
- **feature**
  - 기능 구현 시에 사용
  - 이름 : master, develop, release-\*, hotfix-\* 제외한 이름 가능
  - develop 브랜치에서 분기
  - develop 브랜치로 merge
  - merge 후 삭제
  - merge 할 땐 ```--no--ff```를 사용하여 기록을 그룹화
    - Fast-forward 관계에 있어도 Maege commit 생성하여 해당 브랜치가 존재하였다는 정보를 남길 수 있음
    - commit 기록을 되돌리기 편함
    - 사용하지 않으면, 브랜치의 존재 여부를 몰라 어떤 commit 부터 해당 기능을 구현했는지 확인 어려움
- **release**
  - 다음 버전 출시를 위해 QA를 진행하는 브랜치
  - 버그 수정 및 버전 번호, 빌드 날짜와 같은 메타 데이터를 준비하며 <u>기능 개발 금지</u>
  - 이름 : release-*
  - develop 브랜치에서 분기
  - develop 브랜치로 merge
  - merge 할 땐 ```--no--ff```를 사용하여 기록을 그룹화
  - master로 merge 후에는 tag 명령을 통해 버전 명시
- **hotfix** : 버그 수정
  - 버그 발생 시 빠른 수정을 위해 생성하는 브랜치
  - 버그 수정 중에도 develop에서 계속 개발할 수 있다는 장점
  - 이름 : hotfix-*
  - master 브랜치에서 분기
  - master 브랜치로 merge
  - master로 merge 후에는 tag 명령으로 이전 버전 보다 높은 버전 명시

# 어플리케이션 아키텍처

## 계층형 구조 
### Controller
- 웹 계층

### Service
- 비즈니스 로직, 트랜잭션 처리

### Repository
- JPA를 직접 사용하는 계층, 엔티티 매니저 사용

### Domain
- 엔티티가 모여 있는 계층, 모든 계층에서 사용

## 패키지 구조
- config
- controller
- domain
- exception
- repository
- request
- response
- service

# Lombok
- 보일러 플레이트 코드를 줄여주는 라이브러리
- 종류
  - ```@Setter```, ```@Getter```
  - ```@ToString```
  - ```@NoArgsConstructor``` : 기본 생성자 자동 생성
  - ```@AllArgsConstructor``` : 모든 매개변수 생성자 자동 생성
  - ```@RequiredArgsConstructor``` : 필수 매개변수 생성자 자동 생성
  - ```@Data``` : Setter + Getter + ToString + Equals + hashCode + RequiredArgsConstructor + Value => **실무에서는 주의해서 사용**
  - ```@Builder``` : 빌더 패턴 자동 생성
  - ```@Slf4j``` : 해당 클래스 logger 자동 생성
  - ```@UtilityClass``` : static method만 제공하는 유틸리티 성격의 클래스들의 생성자를 private으로 만들어서 객체 생성을 할 수 없도록 함

# HTTP Protocol
- HTTP (Hyper Text Transfer Protocol)
  - 단순 텍스트가 아닌 하이퍼 텍스트(다른 내용에 대한 링크를 갖는 문자열)를 전송하기 위한 프로토콜

### HTTP Request Message
- 첫째줄 : [HTTP Method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
  - GET
  - PUT
  - POST
  - DELETE
- 두번째줄 ~ 줄바꿈 전 : Header
  - User-Agent
  - Accept
- 줄바꿈 이후 : Request Body

### HTTP Response Message
- 첫째줄 : [Status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
  - 100 : Informational 
  - 200 : Successful
  - 300 : Redirection
  - 400 : Client error
  - 500 : Server error
- 두번째줄 ~ 줄바꿈 전 : Header
- 줄바꿈 이후 : Response Body

# Transaction
### ACID
  - **Atomic (원자성)**
    - All or Nothing, 모든 작업이 실행되거나 혹은 모두 실행되지 않아야 함
  - **Consistency (일관성)**
    - 모든 트랜잭션이 종료된 후에는 DB의 제약조건을 모두 지키고 있는 상태가 되어야 함
  - **Isolation (격리성)**
    - 트랜잭션은 다른 트랜잭션과 독립적으로 동작해야 함
    - A 트랜잭션이 하는 일을 B 트랜잭션은 모르게 해야함
    - 하지만 현실은...
      - 성능과 안정성의 트레이드 오프 관계
      - READ_UNCOMMITTED > READ_COMMITTED > **REPEATABLE_READ** > SERIALIZABLE 순서로 성능은 떨어지고 격리성(고립성)은 증가한다.
        - [트랜잭션 격리 수준(isolation level)](https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation)
        - 격리성이 낮을 때 일어나는 문제: Dirty read, Phantom read 등 발생
    - 일반적으로는 MYSQL InnoDB의 기본 값인 REPEATABLE_READ를 많이 활용
  - **Durability (지속성)**
    - commit을 하게 되면 지속(저장)이 되어야 함
    - DB 저장이 실패하더라도 모든 로그를 모두 남겨 DB에 순차적으로 모두 반영이 되도록 해야함

# 캐시
- 임시로 데이터를 저장하는 공간
- for 성능 향상

## Redis
- Key-value
- 인 메모리 데이터 스토어 -> 휘발성 + Redis 자체 영속성 지원(안정성 낮음)
- 캐시 서버 용도로 사용됨
- 다양한 형태의 데이터 타입 지원
- 같은 요청이 여러번 들어오는 경우 **성능 향상**
- SpinLock을 활용한 **동시성 제어**
- 캐시 메모리 사용이 적합한 지 판단 필요
  1. 동일한 요청이 자주 들어오는가?
  2. 자주 변경되는 데이터 인가?


### [redis 명령어](http://redisgate.kr/redis/introduction/redis_intro.php)
  ```shell  
    $ set [KEY] [VALUE]
    $ get [KEY]
    $ del [KEY]
    $ keys *
  ```

# Kafka
## Message Oriented Middleware
- 독립된 서비스 간 데이터를 주고받을 수 있는 형태의 미들웨어
- 서비스 간 결합도 낮추기 위함
- **Kafka**(대용량 처리 특화), **RabiitMQ**(라우팅 가능) 등

## Message Queue
- 사용 이유
  - Asynchronous(비동기 처리)
  - Decoupling(탈동조화)
  - Scalability(확장성)
- 모델
  - **Point to Point** :: RabiitMQ
    - [Sender] - [Message Queue] - [Receiver] * n
      - **fire and forget** :: 메시지 유실 가능성 높음
      - **request reply** :: 메시지 유실 가능성 낮음. request queue, response queue 존재
  - **Pub/Sub** :: **kafka**, RabiitMQ
    - [Publisher/Producer] - [Message Queue] - [Subscriber/Consumer]
    - Topic : 이벤트를 분류하는 단위. 한 개 이상의 파티션으로 이루어짐
      - Ack
        - 0 : Producer -> Leader Partition에 전송 후 응답값 받지 않음(처리 속도 빠름, 메시지 유실 가능성 있음)
        - 1 : Producer -> Leader Partition에 전송 후 Leader Partition의 응답 받음(중간)
        - all : Producer -> Leader Partition에 전송 후 Replication 정상 응답 까지 기다림(처리 속도 느림, 메시지 유실 가능성 없음)
    - Disk I/O
    - Page Cache를 통해 성능 개선
    - 메모리를 많이 잡아 먹음 -> 분리 필요
    - 데이터 전송 배치 처리 (데이터 묶어서 처리) -> 리소스 소모 최소화
    - [Kafka Streams](https://velog.io/@holicme7/Apache-Kafka-Kafka-Streams-%EB%9E%80)
      - Kafka 내부 파이프라인
    - Zookeeper : Kafka 메타데이터 관리 툴. Broker 관리
    - Broker : 실제 데이터 저장 장소

# [NginX](https://blog.naver.com/gi_balja/223028077537)
- 최근 가장 범용적으로 사용되는 웹 서버 프로그램
- 가벼움
- 비동기, 논 블로킹, 매번 프로세스 생성 X => 많은 요청이 들어와도 좋은 성능을 보여줌
- REQ -> TASK -> [TASKS QUEUE] -> work process
- 프록시 서버로 많이 쓰임
  - **forward proxy** : 클라이언트 뒤에 붙어 들어오는 요청을 받아줌
    - 반복 요청이 많은 경우 캐싱 가능
    - 서버가 클라이언트의 정보를 알기 어려움 -> 개인정보 누설 방지
  - **reverse proxy** : 요청을 분산하여 서버에 내려줌. 로드밸런서의 역할. DMZ망
    - 보안 상 이점 : 유저가 서버에 직접 붙을 수 없음
    - 

# Test
- 이전
  - 노동 집약적인 테스트 진행
    - 자동화된 테스트가 어려운 코드들이 많았음(SQL 중심의 코드)
    - 1회성 개발이 많아 코드의 품질 보다는 기능적인 완성도만 체크
    - FE/BE가 나눠져 있지 않고, 서버에서 로직과 화면까지 모두 만들어서 보내는 구조가 많아 테스트 자동화라고 해도 실제 화면에서 직접 마우스/키보드가 움직이는 것 처럼 하는 테스트가 많았음
- 현재
  - 자동화
  - 빠름
  - 여러가지 테스트와 방법론 등 테스트의 발전
- **테스트를 잘하면 좋은 점**
  - 셀프 코드 리뷰
  - 테스트 하기 어려운 경우 코드가 잘못된 것을 빨리 알아챌 수 있음
    - 너무 다양한 일을 하고 역할이 많은 경우
  - 테스트가 잘 되어 있으면 리팩토링도 쉬워짐
    - 코드의 품질이 좋아짐
    - 현재 코드의 아주 세부적인 정책들이 모두 테스트로 문서화됨
    - 다음에 기능을 추가하거나 변경할 때도 더 편하게 기존 기능의 영향도를 알 수 있음
  - 결론 : 장기적으로 볼 때 더 빠르고 안정적인 개발이 가능하게 됨
- **테스트를 잘 하려면**
  - 클래스나 메서드가 SRP를 지키고 너무 크지 않아야 한다
  - 유닛 테스트의 경우 적절한 Mocking으로 격리성 확보

## JUnit
- 단위 테스트를 실행하고 결과를 검증해서 전체 결과를 리포트 해주는 프레임 워크
- 사용자가 직접 동작시킬 수도 있으면 Gradle이나 Maven 등을 통해 빌드하면서 테스트 가능
- spring-boot-starter-test에 기본적으로 JUnit5 포함

### Mockito
```java

  @ExtendWith(MockitoExtension.class)
  class XXXServiceTest {

    @Mock
    private XXXRepository xxxRepository;

    @InjectMocks
    private XXXService xxxService;

    @Test
    void testXXX() {

      //given
      given(xxxRepository.findById(anyLong()))                // 이걸 실행하면
              .WillReturn(Optional.of(XXX.builer().build())); // 이게 나옴(가짜)

      //when
      XXX xxx = xxxService.getXXX(234L);

      //then
      assertEquals("xxx", xxx.getXXX());
    }
  }

```

### Controller 테스트 방법
- ```@SpringBootTest``` + ```@AutoConfigureMockMvc```
  - 전체 Bean 모두 생성 후
  - mockMvc를 통해 http요청과 검증 진행
- ```@WebMvcTest```
  - 필요한 MVC 관련 Bean만 생성
    - Controller, ControllerAdvice, Converter, Filter, HandlerInterceptor 등
    - Service 등 Controller에서 의존하는 하위 레이어의 기능은 @MockBean을 통해 모킹해서 원하는 동작을 하도록 함(Mockito 와 유사)
  
```java

  @WebMvcTest(XXXController.class)
  class XXXControllerTest {

    @MockBean
    private XXXService xxxService;

    @Autowired
    private MockMvc mockMvc;

    @Test
    void xxxTest() throws Exception {

      //given
      given(xxxService.getXXX(anyLong()))
          .willReturn(XXX.builder().xxx("test").build());

      //when

      //then
      mockMvc.perform(get("/xxx/1"))
              .andDo(print()) // req/res 표시

              .andExpect(jsonPath("$.xxx").value("test"));
      }
    
  }

```

### Service 테스트 방법
- **verify**
  - 의존하고 있는 Mock이 해당되는 동작을 수행했는지 확인
  ```java
    verify(xxxRepository, times(1)).save(any());
    verify(xxxRepository, times(0)).findById(anyLong());
  ```
- **Argumentcaptor**
  - 의존하고 있는 Mock에 전달된 데이터가 내가 의도하는 데이터가 맞는지 검증
  ```java
    ArgumentCaptor<XXX> captor = ArgumentCaptor.forClass(XXX.class);
    verify(xxxRepository, times(1)).save(captor.capture());
    assertEquals("test", captor.getValue().getXXX());
  ```
- **assertions**
  - 다양한 단어(assertion) 방법들
  ```java
    assertEquals("1234", captor.getValue().getXXX());`
    assertNotEquals("1234", captor.getValue().getXXX());
    assertNull(result);
    assertNotNull(result);
    assertTrue(result.getBoolean());
    assertFalse(result.getBoolean());
    assertAll(
      () -> assertTrue(result.getBoolean()),
      () -> assertFalse(result.getBoolean())
    );
  ```
- **assertThrows**
  - 예외를 던지는 로직 테스트
  ```java
    XXXException exception = assertThrows(XXXException.class, () -> 
                                  xxxService.getXXX(123L));
    assertEquals(XXX_NOT_FOUND, exception.getErrorCode());
  ```