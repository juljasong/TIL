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

# Redis
- 사용목적 
  - Memory DB => 빠른 처리, 휘발성 데이터
  - 같은 요청이 여러번 들어오는 경우
  - SpinLock을 활용한 동시성 제어
  - 동시성 제어를 AOP를 활용하여 실습하는데 활용되는 인프라

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