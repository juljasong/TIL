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

# 04. Exception