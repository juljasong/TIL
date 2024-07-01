# Spring Security
: 인증 (Authentication) ,권한(Authorize) 부여 및 보호 기능을 제공하는 프레임워크

## 설정
```java
@EnableWebSecurity(debug = true)
@EnableMethodSecurity(securedEnabled = true, prePostEnabled = true) // @Secured, @PreAuthorize 애노테이션을 기반으로 한 보안을 작동
public class SpringSecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

                // 시큐리티 권한 처리
        http    .authorizeHttpRequests(authorize -> authorize
                    .requestMatchers("/", "/auth/login", "/auth/signup").permitAll()
                    .requestMatchers("/user").hasAnyRole("USER")
                    .requestMatchers("/admin").access(new WebExpressionAuthorizationManager("hasRole('ADMIN') AND hasAuthority('WRITE')"))
                    .anyRequest().authenticated()
                )

                // 로그인
                .formLogin(formLogin -> formLogin
                        // 로그인 화면
                        .loginPage("/login") 
                        // POST login 주소가 호출 되면 시큐리티가 낚아채서 대신 로그인 진행
                        .loginProcessingUrl("/login") 
                        // 인증 후 인증 전 요청한 url로 보내줌
                        .defaultSuccessUrl("/") 
                )

                // 로그아웃
                .logout(logout -> logout
                        .logoutSuccessUrl("/")
                        .invalidateHttpSession(true)
                );

        return http.build();
    }

    // PasswordEncoder
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // Security Session -> Authentication => UserDetails
    @Bean
    public UserDetailsService userDetailsService(UserRepository userRepository) {
        return new UserDetailsService() {
            @Override
            public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
                User user =  userRepository.findByEmail(username).orElseThrow(() -> new UsernameNotFoundException(username + "을 찾을 수 없습니다."));

                return new UserPrincipal(user);
            }
        };
    }
```
- ```@EnableWebSecurity``` : 스프링 시큐리티 필터가 스프링 필터 체인에 등록

### PasswordEncoder
- 종류 
  - NoOpPasswordEncoder : 인코딩 X
  - BCryptPasswordEncoder
  - SCryptPasswordEncoder
  - Argaon2PasswordEncoder

### UserDetails
- 사용자의 정보를 담는 인터페이스
- [구현하기](https://programmer93.tistory.com/6)

### UserDetailsService
- Authentication 생성
- 시큐리티 설정에서 loginProcessingUrl에 명시한 url에 요청이 오면 자동으로 UserDetailsService 타입으로 IoC 되어 있는 loadUserByUsername 함수 실행

### ```@Secured``` 
```java
@Secured("ROLE_ADMIN")
@GetMapping("/admin")
public String admin() {
    return "admin page";
}
```
- ```@EnableMethodSecurity(securedEnabled = true)```로 활성화
- 애노테이션에 인자로 받은 권한이 유저에게 있을 때만 실행
- 표현식 사용 불가

### ```@PrePostAuthorize```
```java
@PreAuthorize("hasRole('ROLE_COMP') or hasRole('ROLE_ADMIN')")
@GetMapping("/comp")
public String comp() {
    return "comp page";
}
```
- ```@EnableMethodSecurity(prePostEnabled = true)```로 활성화
- ```@PreAuthorize```: 메서드가 실행되기 전에 인증을 거친다.
- ```@PostAuthorize```: 메서드가 실행되고 나서 응답을 보내기 전에 인증을 거친다.
- 사용 가능한 표현식
  - ```@hasRole([role])```@ : 현재 사용자의 권한이 파라미터의 권한과 동일한 경우 true
  - ```@hasAnyRole([role1,role2])```@ : 현재 사용자의 권한 파라미터의 권한 중 일치하는 것이 있는 경우 true
  - ```@principal```@ : 사용자를 증명하는 주요객체(User)를 직접 접근할 수 있다.
  - ```@authentication```@ : SecurityContext에 있는 authentication 객체에 접근 할 수 있다.
  - ```@permitAll```@ : 모든 접근 허용
  - ```@denyAll```@ : 모든 접근 비허용
  - ```@isAnonymous()```@ : 현재 사용자가 익명(비로그인)인 상태인 경우 true
  - ```@isRememberMe()```@ : 현재 사용자가 RememberMe 사용자라면 true
  - ```@isAuthenticated()```@ : 현재 사용자가 익명이 아니라면 (로그인 상태라면) true
  - ```@isFullyAuthenticated()```@ : 현재 사용자가 익명이거나 RememberMe 사용자가 아니라면 true

## CorsFilter
```java
    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true); // 응답 시 json을 js에서 처리할 수 있게 할 지
        config.addAllowedOrigin("*"); // 모든 ip에 응답 허용
        config.addExposedHeader("*"); // 모든 header에 응답 허용
        config.addAllowedMethod("*"); // 모든 http method에 응답 허용
        source.registerCorsConfiguration("/**", config);

        return new CorsFilter(source);
    }
```
- cors 설정
- vs ```@CrossOrigin```
  - 인증 X일 때만 가능
  - 시큐리티 필터에 등록해야 인증 이후 CORS 처리 가능

# Before JWT (Json Web Token)
## Session
- 클라이언트로부터 오는 일련의 요청을 하나의 상태로 보고 그 상태를 일정하게 유지하는 기술
- 클라이언트가 웹 서버에 접속해있는 상태가 하나의 단위
- [쿠키와 세션의 개념](https://interconnection.tistory.com/74)
- 서버의 저장소를 사용하기 때문에 트래픽이 많아짐에 따라 서버에 부하를 줄 수 있음

## TCP
- OSI 7계층
  - 응용 
  - 표현 - 암호화, 압축
  - 세션 - 인증 체크
  - 전송 - TCP/UDP
  - 네트워크 - IP
  - 데이터링크 - WAN/LAN
  - 물리 - 광 케이블

## CIA
- **기밀성 ( Confidentiality )**
  - 정보를 오직 인가된 사용자에게만 허가
  - 개인정보나 지적 재산권 등 가치 있는 정보
  - 기업보유 핵심기술 / 민원인들에 대한 개인정보
- **무결성 ( Integrity )**
  - 부적절한 정보 변경이나 파기 없이 정확하고 완전하게 보존
- **가용성 ( Availability )**
  - 시기적절하면서 신뢰할 수 있는 정보로 접근과 사용할 수 있음
- 암호화 필요
  
## [RSA](https://ko.wikipedia.org/wiki/RSA_%EC%95%94%ED%98%B8)
- public key : 공개키 
- private key : 개인키
- 공개키 -> 개인키 : 암호화
- 개인키 -> 공개키 : 전자서명

1. 문서를 받아서
2. A의 공개키로 열어봄
    - 열리면 -> 인증 O
      - B의 개인키로 열어봄 => 내용 확인
    - 안열리면 -> 인증 X 

## RFC(Request for Comments) 문서
- [RFC란?](https://ko.wikipedia.org/wiki/RFC)
- 컴퓨터 네트워크 공학 등에서 **인터넷 기술**에 적용 가능한 새로운 연구, 혁신, 기법 등을 아우르는 메모

# JWT( Json Web Token )
- [공식 사이트](https://jwt.io/)
- [디버거](https://jwt.io/#debugger-io)
- **Header**
  ```
  {
    "alg": "HS256",
    "typ": "JWT"
  }
  ```
  - 토큰 유형(JWT)
  - 사용되는 서명 알고리즘(예: HMAC SHA256 또는 RSA)
  - Base64Url
- **Payload**
  ```
  {
    "sub": "1234567890",
    "name": "John Doe",
    "admin": true
  }
  ```
  - 클레임을 포함하는 페이로드
    - Registered claims (옵션)
      - iss (발행자), exp (만료 시간), sub (주제), aud (청중)
    - Public claims
    - Private claims
      -  사용에 동의한 당사자 간에 정보를 공유하기 위해 생성된 맞춤 클레임
  - Base64Url(누구나 읽기 가능)
- **Signature**
  - 메시지가 도중에 변경되지 않았는지 확인
  - 개인 키로 서명된 토큰의 경우 JWT의 보낸 사람이 누구인지 확인 가능

## Bearer 인증 방식
### HTTP 기본 인증
- HTTP header ( Authorization ) 를 사용하는 인증 방법
- username:password을 Base64로 인코딩
- 요청을 가로채서 username과 password 탈취 혹은 수정이 가능 => HTTPS 통신 필요 

### Authorization
```
Authorization: <type> <credentials>
```
- \<type\>
  - **Basic**
    - 사용자 아이디와 암호를 Base64로 인코딩한 값을 토큰으로 사용한다. (RFC 7617)
  - **Bearer**
    - JWT 혹은 OAuth에 대한 토큰을 사용한다. (RFC 6750)
  - **Digest**
    - 서버에서 난수 데이터 문자열을 클라이언트에 보낸다. 클라이언트는 사용자 정보와 nonce를 포함하는 해시값을 사용하여 응답한다 (RFC 7616)
  - **HOBA**
    - 전자 서명 기반 인증 (RFC 7486)
  - **Mutual**
    - 암호를 이용한 클라이언트-서버 상호 인증 (draft-ietf-httpauth-mutual)
  - **AWS4-HMAC-SHA256**
    - AWS 전자 서명 기반 인증 (링크)

### [토큰의 장단점](https://velog.io/@cada/%ED%86%A0%EA%B7%BC-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D%EC%97%90%EC%84%9C-bearer%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)
- 장점
  - 헤더와 페이로드를 가지고 서명 필드를 생성하므로 데이터 변조 후 재전송을 막을 수 있습니다.
  - stateless 서버를 만들 수 있습니다.
  - 모바일 어플리케이션에서도 잘 동작합니다.
  - 인증정보를 다른 웹서비스에 전송할 수 있습니다. (OAuth)
- 단점
  - 여전히 누구나 디코딩이 가능하므로 데이터 유출이 발생할 수 있습니다.
  - 토큰을 탈취당할 경우, 대처하기 어렵다. (유효기간을 기다리거나 token refresh를 해야한다)
  - JWT의 경우, 토큰의 길이가 길기 때문에 요청이 많아질수록 서버 자원의 낭비가 많아진다.
  
### Session
- 장점
  - 세션 ID 자체에는 아무런 의미가 없어 탈취되어도 해석할 수 없음
- 단점
  - 하이재킹 가능
    - HTTPS 사용하여 요청 자체를 탈취해도 안의 정보를 읽기 힘들게 하거나,
    - 세션에 유효시간을 넣어서 방지
  - 세션 객체가 서버에 저장되어 사용자가 많아질 수록 부하가 걸림
  - 확장성이 좋지 않음
    - 여러 대의 서버 컴퓨터를 추가할 경우 각 서버 마다 세션 정보 저장해야 함
    - 확장 시 모든 서버가 접근할 수 있도록 별도의 중앙 세션 관리 시스템 필요