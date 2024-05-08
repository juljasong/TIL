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