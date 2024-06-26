## 객체 지향 프로그래밍

- 컴퓨터 프로그램을 명령어의 목록으로 보는 시각에서 벗어나 여러 개의 독립된 단위, 즉 **객체**들의 모임으로 파악하고자 하는 것. 
- 각각의 객체는 메시지를 주고 받고, 데이터를 처리할 수 있음. (협력)
- 프로그램을 **유연**하고 **변경**이 용이하게 만들기 때문에 대규모 소프트웨어 개발에 많이 사용됨.
  - 컴포넌트를 쉽고 유연하게 변경하면서 개발할 수 있는 방법 => **다형성**

### 다형성
- **역할(인터페이스)** 과 **구현(인터페이스를 구현한 클래스, 구현 객체)** 분리
  - 단순해지고, 유연해지며 변경도 편리해짐
  - 확장 가능한 설계
  - 클라이언트에 영향 주지 않는 변경 가능
  - 인터페이스를 안정적으로 잘 설계하는 것이 중요
- 장점
  - 클라이언트는 대상의 역할(인터페이스)만 알면 됨
  - 클라이언트는 구현 대상의 내부 구조를 몰라도 됨
  - 클라이언트는 구현 대상의 내부 구조가 변경되어도 영향 받지 않음
  - 클라이언트는 구현 대상 자체를 변경해도 영향을 받지 않음
- 객체 설계 시 역할(인터페이스) 먼저 부여하고, 그 역할을 수행하는 구현 객체 만들기
- 자바 언어의 다형성
  - 오버라이딩
- Spring은 다형성을 극대화해서 이용할 수 있게 해줌
- Spring에서 이야기하는 제어의 역전(IoC), 의존관계 주입(DI)은 다형성을 활용해 역할과 구현을 편리하게 다룰 수 잇도록 지원


## SOLID
### SRP : 단일 책임 원칙 (Single Responsibility Principle)
- 한 클래스는 하나의 책임만 가져야 한다.
  - 모호한 기준
- 중요한 기준은 **변경**. 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것.

### OCP : 개방-폐쇄 원칙 (Open/Closed Principle)
- 소프트웨어 요소는 **확장에는 열려** 있으나 **변경에는 닫혀** 있어야 한다.
- 다형성 활용
- 인터페이스를 구현한 새로운 클래스를 하나 만들어서 새로운 기능 구현
-   ```java
    MemberRepository m = new MemoryMemberRepository();  // 기존
    MemberRepository m = new JdbcMemberRepository();    // 변경
    ``` 
    - 구현 객체를 변경하려면 클라이언트 코드를 변경해야 함.
    - 다형성을 사용했지만 OCP 원칙 지킬 수 없음
    -  **객체 생성하고, 연관관계 맺어주는 별도의 조립, 설정자 필요**

### LSP : 리스코프 치환 원칙 (Liskov Substitution Principle)
- 프로그램 객체는 프로그램의 정확성을 깨트리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 함.
- 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 함 .

### ISP : 인터페이스 분리 원칙 (Interface Segregation Principle)
- 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나 보다 나음
- 인터페이스가 명확해지고, 대체 가능성이 높아짐

### DIP : 의존관계 역전 원칙 (Dependency Inversion Principle)
- 추상화에 의존, 구체화에 의존하면 안됨
- 구현 클래스가 아닌 **인터페이스에 의존**
- SRP의 역할(Role)에 의존해야 하는 것과 같음.
-  ```java
    MemberRepository m = new MemoryMemberRepository();
    ``` 
    - 인터페이스에 의존하지만, 구현 클래스도 동시에 의존함.
    - DIP 위반

### => 다형성 만으로는 OCP, DIP를 지킬 수 없음.

## Spring
- 다음 기술로 다형성 + OCP, DIP 를 가능하게 지원

### IoC : 제어의 역전 (Inversion of Control)
- **프로그램 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것**
- 기존 : 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성, 연결, 실행함 => 구현 객체가 프로그램의 제어 흐름을 조종함
- AppConfig 등장 이후 -> 객체는 자신의 로직을 실행하는 역할만 담당 => 프로그램 제어 흐름은 AppConfig가 가져감.
- 프로그램 제어 흐름에 대한 권한을 모두 AppConfig가 가지고 있음.
- 프레임워크 vs 라이브러리
  - 프레임워크 : 내가 작성한 코드를 제어, 대신 실행
  - 라이브러리 : 내가 작성한 코드가 직접 제어의 흐름을 담당

### DI : 의존 관계 주입 (Dependency Injection)
- 실제 어떤 구현 객체가 사용될 지 모름
- 의존 관계는 **정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계**를 분리해서 생각해야 함
- **정적인 클래스 의존관계**
  - 클래스가 사용하는 import만 보고 의존관계 쉽게 판단 가능.
  - 어플리케이션 실행하지 않아도 분석 할 수 있음.
- **동적인 클래스 의존관계**
  - 어플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계.
  - **실행시점(런타임)**에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 의존관계 주입이라고 함.
  - 객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결.
  - 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상 타입 인스턴스를 변경할 수 있다.
  - 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경 할 수 있다.

### IoC/DI 컨테이너 제공
- AppConfig 처럼 객체를 생성하고 관리하면서 의존 관계를 연결해주는 것
- 의존 관계 주입에 초점을 맞추어 최근에는 주로 DI 컨테이너라고 함
- 어셈블러, 오브젝트 팩토리라고도 함
