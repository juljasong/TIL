# 코드 커버리지
- 테스트 코드가 프로덕션 코드를 얼마나 실행했는지 백분율로 나타내는 지표
- 일반적으로 80%가 좋은 목표
- 높은 코드 커버리지의 장점
  - 과감한 리팩토링 가능 -> 코드에 문제가 있으면 테스트 코드가 알려줄 것
  - 불필요한 프로덕션 코드 삭제
- 100% 커버리지 != Bug-free Software
  - 중요한 로직을 포함하지 못하는 테스트 코드거나
  - 테스트 코드가 문제를 사전에 제대로 발견할만큼 견고하지 않다면
  - 버그가 없다고는 확정할 수 없음

## 종류
### 함수(Function) 커버리지
- 어떤 함수가 최소 1번 이상 호출되었는지를 기준
- 함수 내부의 모든 코드가 실행되었는지는 판단 기준에서 제외

### 구문(Statement)/라인(Line) 커버리지
- 프로덕션 코드의 전체 구문 중 몇 줄의 구문이 실행되었는지를 기준

### 결정(Decision)/브랜치(Branch) 커버리지
- 프로덕션 코드에 조건문이 있는 경우, 조건문의 전체 조건식이 True인 케이스, False인 케이스 2가지가 최소한 한번 실행되면 충족

### 조건(Condition) 커버리지
- 전체 조건식이 아니라 개별 조건식을 기준으로 판단
- 개별 조건식이 모두 참, 거짓을 한번씩 갖도록하면 조건 커버리지 기준을 만족

### 조건/결정(Condition/Decision) 커버리지
- 조건 커버리지와 결정 커버리지 모두를 만족할 때 충족되는 조건
- 개별 조건과 전체 조건 모두 최소 한번식 True/False가 선택되어야 한다.

## TDD(Test-Driven Development, 테스트주도 개발)
- 매우 짧은 개발 사이클을 반복하는 소프트웨어 개발 프로세스 중 하나
- 테스트 > 개발 > 수정 > 테스트 반복
- **장점 : 높은 퀄리티의 소프트웨어를 보장**
  - 보다 튼튼한 객체지향적인 코드 생산 : 코드의 재사용성을 보장할 것을 명시하므로 TDD를 통한 소프트웨어 개발 시 기능 별 철저한 모듈화가 이뤄짐
  - 재설계 시간의 단축 : 테스트 코드를 먼저 작성하기 때문에 내가 지금 무엇을 해야하는지 분명히 정의하고 개발을 시작
  - 디버깅 시간의 단축 : 자동화 된 유닛테스팅을 전재하므로 특정 버그를 손 쉽게 찾아낼 수 있음
  - 테스트 문서의 대체 가능
  - 추가 구현의 용이함 : 자동화된 유닛 테스팅을 전제하여 기존 코드에 기능을 추가할 때 처음부터 테스트 하지 않아도 됨
- **단점 : 생산성의 저하**
  - 테스트 단계가 두 번
  - 테스트 하기 어려운 상황이 올 수 있는데, 이를 피할 수 있는 방법이 있는데 테스트에 얽매여 프로덕트에 집중하지 못하게 됨
  
* 높은 퀄리티의 소프트웨어
  1. 절대 에러나 버그가 없을 것.
  2. 추가적인 요구사항이 있을 때 손쉽게 그 요구사항을 반영할 수 있을 것.
  3. 유지보수에 용이할 것

# System.out.println()
- Java 개발 시 디버깅 용도로 콘솔에 출력하는 메소드 중 하나다.
- **System** : java.lang 패키지의 내장된 최종 클래스
- **out** : System 클래스의 정적 멤버 필드이며 PrintStream
- **println** : PrintStream 클래스의 메서드이고, 표준 콘솔에 전달된 인자와 줄 바꿈을 출력한다.
- **왜 사용하면 안되는가?**
  1. **성능 이슈**
     - **블로킹 I/O**
     - **멀티쓰레드에서 락 발생**
     - System.out.println이 끝날 때 까지 아무 일을 실행할 수 없고 대기해야 하기에 성능을 저하시킬 수 있다.
  2. **로그 레벨 관리 어려움**
     - 로그 레벨을 지정할 수 없기 때문에 디버깅 용도로 사용되는 경우, 어떤 로그 레벨로 출력되는지 확인하기 어려움.
     - 로그 레벨이 제대로 관리되지 않으면 프로덕션 환경에서도 불필요한 디버깅 정보가 출력되어 시스템의 안정성과 보안에 문제가 생길 수 있다.
  3. **유지보수성 저하**
     - 출력 메시지가 코드에 하드코딩되어 있으면 나중에 메시지를 수정하거나 삭제하는 등의 변경 작업이 어려울 수 있다.
  4. **휘발성 로그**
     - 로그 내용이 표준 출력으로 출력되어 파일로 저장되지 않고 휘발이 되어버림
  5. **에러 추적 내용 존재 X**
     - 로그가 발생한 날짜, 시간, 로그 발 생 위치 등과 같은 최소한의 정보들을 남기지 않음

# application.yml 분리
- [Spring YAML 여러 개 쓰기](https://velog.io/@westreed/Spring-application.yml-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0)
- 개발 환경에 따라 application.yml의 값을 따로 지정할 수 있음
- ```application-{profile}.yml``` 형식

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

## Trunk Based
- trunk(main)라는 **단일 브랜치** 위에서 작업
- 신규 피쳐는 main에 바로 커밋 or 며칠 내로 main에 머지할 피쳐 브랜치에서 작업
- main 브랜치에 코드가 머지되면, 먼저 자동화 된 CI시스템이 main 브랜치에 대해 테스트/통합 과정을 통과하는지 확인 후 운영 환경에 배포
- 장점
  - 브랜치 관리에 드는 리소스 절약
  - 며칠 단위로 main에 머지하여, 머지 시 발생하는 변경이 작아짐
    - 컨플릭트 축소
    - 코드 리뷰 용이
  - 배포 프로세스 간단 -> 더 자주 배포할 수 있음
- 단점
  - **큰 기능 문제**
    - 만들고자 하는 기능이 개발 기간이 긴 기능인 경우, 메인 브랜치에 머지하기 어려워짐
  - **의존적인 기능 문제**
    - 만들고자 하는 기능 A가 다른 기능 B의 릴리즈에 의존적이면, B 기능이 완료되기 전 까지 A기능도 배포하기 어려움
  - **불안정한 기능 문제**
    - 기능을 바로 실서버에 배포하여, 잘못된 기능이 메인 브랜치로 머지 될 위험 증가
- 단점을 보완하기 위해..
  - 작은 단위의 배포
  - [피쳐 토글](https://tech.mfort.co.kr/blog/2022-11-24-feature-toggle/)
    - 특정 코드의 실행 여부를 코드 외부에서 제어할 수 있게 하는 시스템
    - 배포와 릴리즈의 분리 
  - 테스트 코드와 통합 단계에서의 테스트 자동화
  

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


# NginX
- [Nginx란 무엇인가?](https://blog.naver.com/gi_balja/223028077537)
- 최근 가장 범용적으로 사용되는 웹 서버 프로그램
- 가벼움
- 비동기, 논 블로킹, 매번 프로세스 생성 X => 많은 요청이 들어와도 좋은 성능을 보여줌
- 동시 처리 잘함
- REQ -> TASK -> [TASKS QUEUE] -> work process
- 프록시 서버로 많이 쓰임
  - **forward proxy** : Clients -> [Forward Proxy] -> Internet -> Resouces Server 
    - 클라이언트 뒤에 붙어 들어오는 요청을 받아줌
    - 반복 요청이 많은 경우 캐싱 가능
    - 서버가 클라이언트의 정보를 알기 어려움 -> 개인정보 누설 방지
  - **reverse proxy** : Clients -> Internet -> [Reverse Proxy] -> Web Server
    - 요청을 분산하여 서버에 내려줌. 로드밸런서의 역할. DMZ망
    - 보안 상 이점 : 유저가 서버에 직접 붙을 수 없음

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