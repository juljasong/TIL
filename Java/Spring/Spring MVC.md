# 01. Spring MVC 전체구조소개
- Spring MVC
  - 현시점 국내 IT 서비스 실무에서 가장 많이 활용되는 웹 개발 기술
  - 웹 개발 기술은 시대에 따라 계속 변화
    - HTML -> CGI -> Servlet -> Spring MVC -> (node, ktor, etc)
- before MVC
  - 스파게티 코드
  - 비즈니스와 화면과 데이터가 분리되지 않아서 너무 많은 역할을 하게 됨
    - R&R이 없음, SRP가 지켜지지 않음
- with MVC
  - 역할의 분담, 자기 할일만 하도록 분리
- M(Model), V(View), C(Controller)
  - Model: 로직 안에서 이동하고 있는 **데이터**
  - View: **화면**은 화면의 역할만
  - Controller: 비즈니스 로직을 처리하고 모델과 뷰를 응답으로 준다
- 모든 분야에서 복잡해지면 분업화가 발생

# 02. HTTP Request&Response
- Controller: 응답값이 기본적으로 HTML을 주도록 되어 있음
- RestController: 응답값으로 Rest API 요청에 대한 응답(주로 JSON)을 주도록 되어 있음
- 매핑 어노테이션
  - ```@RequestMapping```: GET, POST 등 요청 방식을 직접 지정
- 축약형 매핑 어노테이션
  - ```@GetMapping```: 데이터 가져옴
  - ```@PostMapping```: 데이터 전송
  - ```@PutMapping```: 전체 수정
  - ```@PatchMapping```: 일부 수정
  - ```@DeleteMapping```: 삭제

# 03. Filter, Interceptor
![Filter, Interceptor](/Java/Spring/images/filterInterceptor.png)
## Filter
```java

  @Configuration
  public class WebConfig {

    @Bean
    public FilterRegistrationBean loggingFilter() {
      FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
      filterRegistrationBean.setFilter(new LogFilter());
      filterRegistrationBean.setOrder(1);
      filterRegistrationBean.addUrlPatterns("/*");

      return filterRegistrationBean;
    }
  }


  @Slf4j
  public class LogFilter implements Filter {
    
    @Override
    public void doFilter(
      ServletRequest req, ServletResponse res, FilterChain chain
      ) throws ServletException, IOException {

      log.info("Start Filter : " + Thread.currentThread());
      chain.doFilter(req, res);
      log.info("End Filter : " + Thread.currentThread());
    }
  }

```
- 스프링 외부의 **서블릿**에서 제공하는 공통 처리 기능
- 스프링 내로 요청이 들어오기 전 / 스프링 요청이 나갈 때 처리 가능
- 조금 더 **low level** 처리 가능
- doFilter

## Interceptor
```java

 @Configuration
  public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptor(InterceptorRegistry registry) {
      registry.addInterceptor(new LogInterceptor())
              .order(1)
              .addPathPatterns("/**")
              .excludePathPatterns("/css/*", "/images/*");
    }
  }

  @Slf4j
  public class LogInterceptor implements HandlerInterceptor {
    
    // 핸들링 전
    @Overrice
    public boolean preHandle(
      HttpServletRequest req, HttpServletResponse res, Object handler
      ) throws Exception {
      log.info("preHandle Interceptor : " + Thread.currentThread());
      log.info("preHandle handle : " + handler);
      return true;
    }

    // 핸들링 후
    @Overrice
    public void postHandle(
      HttpServletRequest req, HttpServletResponse res, Object handler, ModelAndView modelAndView
      ) {
      log.info("postHandle Interceptor : " + Thread.currentThread());
      return true;
    }

    // finally (Exception이 나서 중단되어도 실행)
    @Overrice
    public void afterCompletion(
      HttpServletRequest req, HttpServletResponse res, Object handler, Exception ex
      ) {
      log.info("afterCompletion Interceptor : " + Thread.currentThread());
      
      if (ex != null) {
        log.error("afterCompletion exception : " + ex.getMessage());
      }
    }

  }

```
- 스프링에서 제공하는 공통 처리 기능
- 실제 매핑된 handler 정보 확인 가능(어떤 것이 실제 내 요청을 처리하는지도 확인 가능)
- 조금 더 상세한 조건식과, 세부적인 스펙(pre, post, after)를 통해 구체적인 시점에 구체적인 동작 가능
- AOP와 비교하면 AOP는 인터셉터 보다 더 구체적인 조건(애노테이션, 파라미터, 주소 등)과 동작 위치(afterThrowing 등)을 가짐

# 04. Exception
- 스프링 MVS 에서 예외를 처리하는 방법(REST API용)
- @ExceptionHandler
  - 컨트롤러 기반 예외 처리
  - HTTP Status code를 변경하는 방법
    - @ResponseStatus
    - ResponseEntity 활용
  - 예외처리 우선 순위
    1. 해당 Exception이 정확히 지정된 Handler
    2. 해당 Exception의 부모 예외 Handler
    3. 이도 저도 아니면 그냥 Exception(모든 예외의 부모)

## 컨트롤러 기반 예외 처리 & @ResponseStatus
```java
@Getter
public class ErrorResponse {
    private final String code;
    private final String message;

    private final Map<String, String> validation;

    public void addValidation(String fieldName, String errorMessage) {
        this.validation.put(fieldName, errorMessage);
    }

    @Builder
    public ErrorResponse(String code, String message, Map<String, String> validation) {
        this.code = code;
        this.message = message;
        this.validation = validation != null ? validation : new HashMap<>();
    }
}

@RestControllerAdvice
@Slf4j
public class ExceptionController {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseBody
    public ErrorResponse invalidExceptionHandler(MethodArgumentNotValidException e) {

        ErrorResponse errorResponse = ErrorResponse.builder()
                .code("400")
                .message("잘못된 요청입니다.")
                .build();

        for (FieldError fieldError : e.getFieldErrors()) {
            errorResponse.addValidation(fieldError.getField(), fieldError.getDefaultMessage());
        }

        return errorResponse;
    }
}
```

## ResponseEntity
```java

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> invalidExceptionHandler(MethodArgumentNotValidException e) {

        ErrorResponse errorResponse = ErrorResponse.builder()
                .code("400")
                .message("잘못된 요청입니다.")
                .build();

        for (FieldError fieldError : e.getFieldErrors()) {
            errorResponse.addValidation(fieldError.getField(), fieldError.getDefaultMessage());
        }

        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                             .header("newHeader", "values..")
                             .body(errorResponse);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(CommonCustomException e) {
      log.error("CommonCustomException is occurred. ", e);

      return ResponseEntity
              .status(HttpStatus.INTERNAL_SERVER_ERROR)
              .body(new ErrorResponse(ErrorCode.INTERNAL_SERVER_ERROR, "Exception is occurred."));
    }
```

## Custom Exception
```java
public enum ErrorCode {
  INTERNAL_SERVER_ERROR(500),
  TOO_BIG_ID_ERROR(507),
  TOO_SMALL_ID_ERROR(507);

  // 문자열을 저장할 필드
  private int code;

  // 생성자 (싱글톤)
  private code(int code) {
    this.code = code;
  }

  // Getter
  public String getCode() {
    return code;
  }
}

public class AlreadyExistsEmailException extends CommonException {
  private static final String MESSAGE = "이미 등록된 이메일 입니다.";

  public AlreadyExistsEmailException() {
    super(MESSAGE);
  }

  @Override
  public int getStatusCode() {
    return 400;
  }
}

  @Getter
  public abstract class CommonException extends RuntimeException {

    public final Map<String, String> validation = new HashMap<>();

    public CommonException(String message) {
      super(message);
    }

    public CommonException(String message, Throwable cause) {
      super(message, cause);
    }

    public abstract int getStatusCode();

    public void addValidation(String fieldName, String message) {
      validation.put(fieldName, message);
    }
}
```

## @RestControllerAdvice
- 어플리케이션의 전역적 예외 처리
- @ControllerAdvice랑 차이는?
  - Controller vs RestController 차이와 동일
  - @ControllerAdvice: 기본적으로 view 응답
  - @RestControllerAdvice: REST API 용으로 객체를 응답(주로 JSON)
  - 일관적인 예외 및 응답 처리를 위해 Spring 백엔드 개발에서 현재 가장 많이 활용되는 기술

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseBody
    public ErrorResponse invalidExceptionHandler(MethodArgumentNotValidException e) {

        ErrorResponse errorResponse = ErrorResponse.builder()
                .code("400")
                .message("잘못된 요청입니다.")
                .build();

        for (FieldError fieldError : e.getFieldErrors()) {
            errorResponse.addValidation(fieldError.getField(), fieldError.getDefaultMessage());
        }

        return errorResponse;
    }

    ...
}
```