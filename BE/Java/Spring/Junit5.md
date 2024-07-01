# Junit5
- Java 개발자가 가장 많이 사용하는 테스팅 프레임워크
  
## 기본 애노테이션
- ```@Test```
- ```@BeforeAll```/```@AfterAll```
- ```@BeforeEach```/```@AfterEach```
- ```@Disabled```
  - @Test 애노테이션 무효화
- ```@DisplayName``` 
  - 테스트 이름 표기

## Assertion
- assertNotNull(actual)
  - 실제 값이 기대한 값과 같은지 확인 
- assertEquals(expeted, actual, ["실패 시 출력 메시지"])
  - 값이 null이 아닌지 확인 
- assertTrue(boolean)
  - 다음 조건이 참(true)인지 확인 
- assertAll(executables...)
  - 모든 확인 구문 확인 (rambda)
- assertThrows(expectedType, executable)
  - 예외 발생 확인 
- assertTimeout(duration, executable)
  - 특정 시간 안에 실행이 완료되는지 확인
