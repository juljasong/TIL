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

## HTTP Request Message
- 첫째줄 : [HTTP Method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
  - GET
  - PUT
  - POST
  - DELETE
  - OPTIONS
- 두번째줄 ~ 줄바꿈 전 : Header
  - User-Agent
  - Accept
- 줄바꿈 이후 : Request Body

## HTTP Response Message
- 첫째줄 : [Status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
  - 100
  - 200
  - 300
  - 400
  - 500
- 두번째줄 ~ 줄바꿈 전 : Header
- 줄바꿈 이후 : Response Body

# Transaction

# Embedded Redis

# Test