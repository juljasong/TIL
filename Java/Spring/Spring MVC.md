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