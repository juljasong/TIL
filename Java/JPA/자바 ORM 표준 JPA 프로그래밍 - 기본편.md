# JPA(Java Persistence API)

- JDBC → MyBatis, JdbcTemplate → JPA
- Java 진영 ORM 기술 표준
  - Object-relational mapping(객체 관계 매핑)
- JPA 2.1 표준명세 구현한 3가지 구현체 : Hibernate, EclipseLink, DataNucleus
- 사용이유
  - SQL 중심 개발 → 객체 중심 개발
  - 생산성
  - 유지보수
  - 패러다임 불일치 해결
  - 성능
  - 데이터 접근 추상화와 벤더 독립성
  - 표준
- 성능 최적화 기능
  - 1차 캐시와 동일성(identity) 보장
    - 같은 트랜잭션 안에서는 같은 엔티티 반환 → 약간의 성능 향상
    - DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read 보장
  - 트랜잭션을 지원하는 쓰기 지연(Transactional write-behind)
    - 트랜잭션 커밋할 때 까지 INSERT SQL 모아서 JDBC BATCH SQL로 한 번에 SQL 전송
    - UPDATE, DELETE로 인한 로우(ROW)락 시간 최소화
    - 트랜잭션 커밋 시 UPDATE, DELETE SQL 실행하고 바로 커밋
  - 지연 로딩(Lazy Loading)
    - 지연 로딩 : 객체가 실제 사용될 때 로딩
    - 즉시 로딩 : JOIN SQL로 한 번에 연관된 객체까지 미리 조회

### JPA 구동방식
- EntityManagerFactory 는 하나만 생성해서 App 전체에서 공유
- EntityManager는 쓰레드 간 공유 X (사용하고 버림)
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행
- 조회
  - EntityManager.find() : 단순 조회
  - 객체 그래프 탐색(a.getB().getC())

### INSERT/UPDATE
```java
    @Entity
    @Table(name = "USER") // 테이블명 다를 때
    public class Member {

        @Id
        private Long id;

        @Column(name = "username") // 컬럼명 다를 때
        private String name;

    }
```
```java
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("persistence.name");
    EntityManager em = emf.createEntityManager();
    EntityTransaction tx = em.getTransaction();

    tx.begin();

    try {
        // INSERT
        Member member = new Member();
        member.setId(1L);
        member.setName("test");
        em.persist();

        // UPDATE
        Member findMember = em.find(Member.class, 1L);
        findMember.setName("test");
        
        // DELETE
        Member findMember = em.find(Member.class, 1L);
        em.remove(findMember);
        em.persist();

        // SELECT(단순조회)
        Member findMember = em.find(Member.class, 1L);

        // SELECT(JPQL) - Member는 Table이 아닌 Object로
        List<Member> findMembers = 
                        em.createQuery("select m from Member as m", Member.class)
                        .getResultList();

        tx.commit();
    } catch (Exception e) {
        tx.rollback();
    } finally {
        em.close();
    }
    emf.close();
```

### JPQL
- SQL을 추상화 한 객체 지향 쿼리 언어
- JPA는 검색할 때 테이블이 아닌 엔티티 객체를 대상으로 검색
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능 → 필요한 데이터만 DB에서 불러오려면 검색 조건 포함된 SQL 필요
- SQL과 유사한 문법, SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
- SQL을 추상화해서 특정 데이터베이스 SQL에 의존 X

## 영속성 컨텍스트
- 엔티티를 영구 저장하는 환경
- EntityManager.persist(entity)
- EntityMaganer를 통해 영속성 컨텍스트에 접근

### 엔티티의 생명주기
```java
    // 비영속 (그냥 객체)
    Member member = new Member();
    member.setId(10L);
    member.setName("test");

    // 영속
    em.persist(member);

    // 준영속
    em.detach(member);

    // 삭제
    em.remove(member);

    // flush - 쿼리 실행
    tx.commit();
```

- **비영속(new/transient)**
  - 영속성 컨텍스트와 전혀 관계 없는 새로운 상태
- **영속(managed)**
  - 영속성 컨텍스트에 관리되는 상태
- **준영속(detached)**
  - 영속성 컨텍스트에 저장되었다가 분리된 상태(영속 → 준영속)
  - 영속성 컨텍스트가 제공하는 기능 사용 불가
  - ```em.detach(entity)```, ```em.clear()```,  ```em.close()```
- **삭제(removed)**
  - 삭제된 상태
- **플러시 (flush)**
  - 영속성 컨텍스트의 변경내용을 데이터베이스에 반영
  - 영속성 컨텍스트를 비우지 않음
  - 변경 감지 → 수정된 엔티티 쓰기 지연 SQL 저장소에 등록 → 데이터베이스에 전송
  - 영속성 컨텍스트를 플러시하는 방법
    - ```em.flush()``` : 직접 호출
    - 트랜잭션 커밋, JPQL 쿼리 실행 : 자동 호출

## Entity Mapping
- 객체 - 테이블 : ```@Entity```, ```@Table```
- 필드 - 컬럼 : ```@Column```
- 기본키 : ```@Id```
- 연관관계 : ```@ManyToOne```, ```@JoinColum```

### @Entity
- JPA가 관리하는 Entity
- 주의사항
  - 기본 생성자 필수(파라미터 없는 public, protected 생성자)
  - final 클래스, enum, interface, inner 클래스 사용 X
  - 저장할 필드에 final 사용 X
- 속성
  - ```@Entity(name = "entity")```
    - JPA에서 사용할 엔티티 이름 지정
    - 기본값 : 클래스 이름 그대로 사용
    - 같은 클래스 이름이 없으면 가급적 기본값 사용할 것
  - ```@Table(name = "TABLE_NAME")``` : 매핑할 테이블 이름
  - ```@Table(catalog = "CATALOG")``` : 데이터베이스 catalog 매핑
  - ```@Table(schema = "SCHEMA")``` : 데이터베이스 schema 매핑
  - ```@Table(uniqueConstraints = {@UniqueCpnstraint( name = "LOGIN_ID_UNIQUE", columnNames = {"LOGIN_ID"} )})``` : DDL 생성 시 유니크 제약 조건 생성

### 데이터베이스 스키마 자동 생성
- DDL을 애플리케이션 실행 시점에 자동 생성
- 테이블 중심 → 객체 중심
- 데이터베이스 방언 활용하여 데이터베이스에 맞는 적절한 DDL 생성
- 개발 장비에서만 사용 (운영 사용 X)
-  **hibernate.hbm2ddl.auto**
   -  create : 기존 테이블 삭제 후 다시 생성(DROP + CREATE)
   -  crate-drop : 종료 시점에 테이블 DROP 
   -  update : 변경분만 반영(운영DB 사용X) // 컬럼 추가 O 컬럼 삭제 X
   -  validate : 엔티티와 테이블이 정상 매핑되었는지만 확인(안맞으면 에러남)
   -  none(default) : 사용하지 않음
-  주의
   -  **운영 장비에서는 create, create-drop, update 사용 XX**
   -  개발 초기 : create / udpate
   -  테스트 서버 : update / validate
   -  스테이징, 운영 서버 : validate / none
  
### DDL 생성 기능
- 제약조건 추가
  - ```@Colum(name = "LOGIN_ID", unique = true, nullable = false, length = 10)```
    - name
    - unique
    - nullable
    - length
  - ```@Table(uniqueConstraints = {@UniqueCpnstraint( name = "LOGIN_ID_UNIQUE", columnNames = {"LOGIN_ID"} )})``` : DDL 생성 시 유니크 제약 조건 생성
    - uniqueConstraints
- JPA 실행 로직에는 영향 X

### 필드와 컬럼 매핑
- ```@Column``` : 컬럼 매핑
  - name : 필드와 매핑할 테이블의 컬럼 이름 (default : 객체의 필드 이름)
  - insertable, updatable : 등록, 변경 가능 여부 (TRUE)
  - nullable(DDL) : null 값 허용 여부 설정
  - unique(DDL) : 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용
    - 이름을 랜덤으로 붙여서 잘 안씀. uniqueConstraints 사용.
  - columnDefinition(DDL) : 데이터베이스 컬럼 정보를 직접 줄 수 있음
    - ex) varchar(100) default 'EMPTY'
  - length(DDL) : 문자 길이 제약조건, String 타입에만 사용 (255)
  - precision, scale(DDL) : BigDecimal 타입에서 사용(BigInteger에서도 O)
    - precision : 소수점 포함 전체 자릿수
    - scale : 소수 자릿수
    - double, float 타입 적용 X
- ```@Enumerated(EnumType.STRING)``` : ENUM 타입 매핑. ***ORDINAL(ENUM 순서) 사용 X***
- ```@Temporal(TemporalType.DATE/TIME/TIMESTAMP)``` : Date 타입 매핑
  - Java8 이후 LocalDate, LocalDateTime 사용 시 생략 가능
- ```@Lob``` : CLOB(매핑하는 필드 타입이 문자일 때), BLOB(나머지) 매핑
- ```@Transient``` : 매핑 제외(데이터베이스 저장X, 조회X). 메모리 상에서만 임시로 값을 보관할 때.

### 기본키 매핑
- ```@Id``` : 직접 할당
- ```@GeneratedValue(strategy = GenerationType.AUTO/IDENTITY/SEQUENCE/TABLE)``` : 자동 생성
  - IDENTITY : DB에 위임, AUTO_INCREMENT
    - MYSQL, PostgreSQL, SQL Server에서 사용
    - ```em.persist()``` **시점에 즉시 INSERT SQL 실행하고 이후 ID 값 알 수 있음**
  - SEQUENCE : DB 시퀀스 오브젝트 사용, CREATE SEQUENCE 
    - ORACLE
    - ```@SequenceGenerator``` 필요
      - name : 식별자 생성기 이름(필수)
      - sequenceName : DB에 등록되어 있는 시퀀스 이름(default : hibernate_sequence)
      - initialValue : DDL 생성 시에만 사용 (default : 1)
      - allocationSize : 시퀀스 호출에 증가하는 수 ***(default : 50)***
      - catalog, schema : DB catalog, schema 이름
      ```java
      @Entity
      @SequenceGenerator(
          name = "TEST_SEQ_GENERATOR",
          sequenceName = "TEST_SEQ",
          initialValue = 1, allocationSize = 1)
      public class test {

        @Id
        @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "TEST_SEQ_GENERATOR")
        private Long id;
      }
      
      ```

### TABLE 전략
- 키 생성 전용 테이블로 데이터 베이스 시퀀스를 흉내내는 전략
- 장점 : 모든 데이터베이스에 적용 가능
- 단점 : 성능 떨어져서 잘 안씀

    ```java
      @Entity
      @TableGenerator(
          name = "TEST_SEQ_GENERATOR",
          table = "TEST_SEQUENCES",
          pkColumnValue = "TEST_SEQ", allocationSize = 1)
      public class test {

        @Id
        @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "TEST_SEQ_GENERATOR")
        private Long id;
      }
      
    ```
- ```@TableGenerator``` 속성
  - name : 식별자 생성기 이름
  - table : 키생성 테이블명
  - pkColumnName : 시퀀스 컬럼명
  - valueColumnNa : 시퀀스 값 컬럼명
  - pkColumnValue : 키로 사용할 값 이름
  - initialValue : 초기값, 마지막으로 생성된 값 기준
  - allocationSize : 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용)
  - catalog, schema : 데이터베이스 catalog, schema 이름
  - uniqueConstraints(DDL) : 유니크 제약 조건 지정

### 권장 식별자 전략
- 기본키 제약 조건 : null 아님, 유일, **변하면 안됨**
- 미래까지 이 조건을 만족하는 자연키 찾기 어려움 → 대리키(대체키) 사용
- 권장 : Long + 대체키 + 키 생성 전략 사용

## 연관관계 매핑
### 단방향 연관관계
![단방향 연관관계](/Java/JPA/images/image.jpg)
```java
  @Entity
  public class Member {

    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @ManyToOne                        // Member(N) : Team(1)
    @JoinColumn(name = "TEAM_ID")     // Join 걸 컬럼명
    private Team team;

    ...
  }
```
### 양방향 연관관계
![양방향 연관관계](/Java/JPA/images/image-1.jpg)
```java
  @Entity
  public class Team {

    @Id @GeneratedValue
    @Column(name = "TEAM_ID")
    private Long id;

    private String name;

    @OneToMany(mappedBy = "team") // Member의 team과 매핑
    private List<Member> members = new ArrayList<>();

    ...
  }
```
#### 연관관계의 주인과 ***mappedBy***
- 객체와 테이블 간 연관관계를 맺는 차이에 대한 이해
  - 객체 연관관계 : 2개
    - 회원 → 팀 (단방향)
    - 팀 → 회원 (단방향)
  - 테이블 연관관계 : 1개
    - 회원 ↔ 팀 (FK)
- ***연관관계의 주인(Owner)***
  - 양방향 매핑 규칙
    - 객체의 두 관계 중 하나를 연관관계의 주인으로 지정
    - 연관관계의 주인만이 외래 키를 관리(등록, 수정) 
    - 주인이 아닌 쪽은 읽기만 가능
    - mappedBy 속성으로 주인 지정 (주인은 mappedBy 속성 지정 X)
      - 주인
        - ***외래키가 있는 곳(N)*** (수정 시 업데이트가 이루어져야 할 테이블)
#### 주의점
- 자주하는 실수
  - 연관관계 주인의 값을 입력하지 않음
  - 순수 객체 상태를 고려하여 양쪽에 값을 넣어주는게 맞음
  - 연관관계 편의 메소드 생성
    ```java
    public void changeTeam(Team team) {
      this.team = team;
      // 연관관계 주인의 값 넣을 때 주인이 아닌 쪽도 값을 같이 넣어주는 연관관계 편의 메소드
      team.getMembers().add(this); 
    }
    ```
    or (상황에 따라 최선이 다름)
    ```java
    public void addMember(Member member) {
      member.setTeam(this);
      members.add(member);
    }
    ```
  - 양방향 매핑시 무한 루프 조심
    - toString(), lombok, JSON 생성 라이브러리....

#### 정리
- 단방향 매핑으로 이미 연관관계 매핑 완료
- 양방향 매핑은 반대 방향으로 **조회(객체 그래프 탐색) 기능**을 추가하는 것
- JPQL에서 역방향으로 탐색할 일이 많음
- 단방향 매핑 잘 하고 양방향은 필요할 때 추가해도 됨(테이블 영향 X)
- 연관관계의 주인은 비즈니스 로직 기준이 아닌, **외래키의 위치를 기준으로 정해야 함**

## 다양한 연관관계 매핑
### 연관관계 매핑시 고려사항
1. 다중성
   - 다대일 [N:1] : ```@ManyToOne```
   - 일대다 [1:N] : ```@OneToMany```
   - 일대일 [1:1] : ```@OneToOne```
   - ~~다대다 [N:M] : ```@ManyToMany```~~(실무사용 X)
2. 단방향, 양방향
   - 테이블
     - 외래키 하나로 양쪽 조인 가능
     - 사실 방향 개념 X
   - 객체
     - 참조용 필드가 있는 쪽으로만 참조 가능
     - 한쪽만 참조하면 단방향
     - 양쪽이 서로 참조하면 양방향
3. 연관관계의 주인
   - 테이블은 외래키 하나로 두 테이블이 연관관계를 맺음
   - 객체 양방향 관계는 A->B, B->A 처럼 참조가 2군데
   - ***연관관계의 주인***은 외래키를 관리하는 참조
   - ***주인의 반대편***은 외래키에 영향을 주지 않고 단순 조회만 가능


### 다대일 [N:1]
- 다대일 단방향
  - 가장 많이 사용하는 연관관계
  - 다대일의 반대는 일대다
- 다대일 양방향
  - 외래키가 있는 쪽이 연관관계의 주인
  - 양쪽을 서로 참조하도록 개발
  
### 일대다 [1:N] ***-> 다대일 양방향 매핑을 사용하도록..***
- 1이 연관관계의 주인
  - 테이블의 경우, 항상 N 쪽에 외래키가 있음(N이 연관관계의 주인)
- 객체와 테이블의 차이 때문에 반대편 테이블의 외래키를 관리하는 특이한 구조
- ```@JoinColumn```을 사용해야 함
  - 그렇지 않으면 조인 테이블 방식을 사용함(중간에 테이블을 하나 추가함)
- 권장하지 않음
  - 엔티티가 관리하는 외래키가 다른 테이블에 있음
  - 연관관계 관리를 위해 추가로 UPDATE SQL 실행
  - **다대일 양방향 매핑을 사용하도록..**
- **일대다 양방향**
  - ```@JoinColumn(name ="column_name ", insertable=false, updatable=false)```
  - 공식적으로 존재하지 않으나, **읽기 전용 필드**를 사용하여 양방향 처럼 사용하는 방법
  - **다대일 양방향 매핑을 사용하도록..**

### 일대일 [1:1]
- 주 테이블이나 대상 테이블 중 외래키 선택 가능
  - **주 테이블에 외래키** :  다대일 단방향 매핑과 유사
    - 주 객체가 대상 객체의 참조를 가지는 것 처럼 주 테이블에 외래키를 두고 대상 테이블을 찾음
    - 객체지향 개발자 선호
    -  JPA 매핑 편리
    -  장점 
       -  주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인 가능
    -  단점
       -  값이 없으면 외래키에 NULL 허용
  - **대상 테이블에 외래키** : 단방향 관계 지원 X, 양방향 관계는 OK
    - 대상 테이블에 외래키 존재
    - DB 개발자 선호
    - 장점
      - 주 테이블과 대상 테이블을 1:1 에서 1:N 관계로 변경할 때 테이블 구조 유지
    - 단점
      - 프록시 기능의 한계로 **지연 로딩으로 설정해도 항상 즉시 로딩됨**
- 외래키에 DB 유니크(UNI) 제약조건 추가
- 외래키가 있는 곳이 연관관계의 주인, 반대편은 mappedBy 적용

### 다대다 [N:M] ***-> 사용 XX..***
- 관계형 DB는 정규화된 테이블 2개로 다대다 관계 표현 불가
- 연결 테이블을 추가해서 일대다, 다대일 관계로 풀어야 함 -> **연결 테이블을 Entity로 승격시킬 것**
- ```@ManyToMany``` 사용, ```@JoinTable```로 연결테이블 지정
- 한계
  - 실무 사용 X
  - 연결 테이블이 단순 연결만 하고 끝나지 않음
  - 주문시간, 수량 등의 데이터가 추가될 수 있음

### ```@JoinColumn``` 속성
- name : 매핑할 외래키 이름 (default : 참조하는 테이블의 기본키 컬럼명)
- referencedColumnName : 외래키가 참조하는 대상 테이블의 컬럼명(default : 참조하는 테이블의 기본키 컬럼명)
- foreignKey(DDL) : 외래키 제약조건 직정 지정
- unique, nullable, insertable, updatable, columnDefinition, table : ```@Column```과 동일

### ```@ManyToOne```(일대다) 주요 속성 
- optional : false로 설정하면 연관된 엔티티가 항상 있어야 함(default : TRUE)
- fetch : 글로벌 페치 전략 설정
  - ```@ManyToOne=FetchType.EAGER```
- cascade : 영속성 전이 기능 사용
- targetEntity : 연관된 엔티티의 타입 정보 설정. 거의 사용X

### ```@OneToMany```(다대일) 주요 속성
- mappedBy : 연관관계의 주인 필드 선택
- fetch : 글로벌 페치 전략 설정
  - ```@OneToMany=FetchType.LAZY```
- cascade : 영속성 전이 기능 사용
- targetEntity : 연관된 엔티티의 타입 정보 설정. 거의 사용X

## 고급 매핑
### 상속관계 매핑
- 관계형 데이터베이스는 상속 관계 X
- 슈퍼타입 서브타입 관계 모델링 기법 -> 객체 상속과 유사
- 상속관계 매핑: 객체의 상속 구조와 DB의 슈퍼타입 서브타입 관계 매핑
  - 각각 테이블로 변환 -> **조인 전략** (ITEM -JOIN- ALBUM, MOVIE, BOOK 테이블)
    - 장점
      - 테이블 정규화
      - 외래키 참조 무결성 제약조건 활용 가능
      - 저장공간 효율화
    - 단점
      - 조회 시 조인 많이 사용, 성능 저하
      - 조회 쿼리 복잡
      - 데이터 저장 시 INSERT SQL 2번 호출
  - (Default)통합 테이블로 변환 ->**단일 테이블 전략**(ALBUM: ITEM_ID, NAME, PRICE, ARTIST, DIRECTER, ...)
    - 장점
      - 조인 필요 없으므로 일반적으로 조회 성능 빠름
      - 조회 쿼리 단순
    - 단점
      - 자식 엔티티가 매핑한 컬럼은 모두 null 허용
      - 단일 테이블에 모든 것을 저장하여 테이블이 커질 수 있음 -> 조회 성능 저하 유발
  - 서브타입 테이블로 변환 -> ~~구현 클래스마다 테이블 전략~~(ALBUM: ITEM_ID, NAME, PRICE, ARTIST)
    - 장점
      - 서브 타입 명확하게 구분해서 처리할 때 효과적
      - not null 제약조건 사용가능
    - 단점
      - 여러 자식 테이블을 함께 조회할 때 성능이 느림(union all)
      - 자식 테이블을 통합하여 쿼리 날리기 어려움
- 주요 어노테이션
  - ```@Inheritance(strategy=InheritanceType.XXX)```
    - JOINED: 조인 전략 (슈퍼 타입에 작성)
    - SINGLE_TABLE: 단일 테이블 전략
    - TABLE_PER_CLASS: 구현 클래스 마다 테이블 전략
  - ```@DiscriminatorColumn(name="DTYPE")```
  - ```@DiscriminatorValue("entity_name")```

### Mapped Superclass - 매핑 정보 상속
- ```@MappedSuperclass```
  - 공통 매핑 정보 필요할 때 사용
    - 주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용
  - 상속관계 매핑 X
  - 엔티티X, 테이블 매핑 X
  - 부모 클래스를 상속받는 자식 클래스에 매핑 정보만 제공
  - 조회, 검색 불가(em.find(BaseEntity) 불가)
  - 직접 생성해서 사용할 일이 없으므로 추상 클래스 권장


## 프록시와 연관관계 관리
### 프록시
- ```em.find()``` : 데이터베이스를 통해 실제 엔티티 객체 조회
- ```em.getRefernce()``` : 데이터베이스 조회를 미루는 가짜(프록시) 엔티티 객체 조회
- 특징
  - 실제 클래스 상속 받아 만들어짐
  - 실제 클래스와 겉 모양이 같음
  - 사용하는 입장에서는 진짜 객체인지 프록시 객체인지 구분하지 않고 사용하면 됨(이론상)
  - 프록시 객체는 실제 객체의 참조(target)를 보관
  - 프록시 객체를 호출하면 프록시 객체는 실제 객체의 메소드 호출
  - **프록시 객체는 처음 사용할 때 한 번만 초기화**
  - **프록시 객체를 초기화 할 때, 프록시 객체가 실제 엔티티로 바뀌는 것은 아님, 초기화 되면 프록시 객체를 통해 실제 엔티티에 접근 가능**
  - **프록시 객체는 원본 엔티티를 상속 받고, 따라서 타입 체크 시 주의(== 비교가 아닌, ```instance of``` 사용)**
  - **영속성 컨텍스트에 찾는 엔티티가 이미 있으면, em.getReference()를 호출해도 실제 엔티티 반환**
  - ***영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일 때, 프록시를 초기화하면 문제 발생(hibernate.LazyInitializationException)***
- 프록시 확인
  - ```emf.getPersistenceUnitUtil.isLoaded(Object entity)``` : 프록시 인스턴스의 초기화 여부 확인
  - ```entity.getClass().getName()``` : 프록시 클래스 확인
  - ```org.hibernate.Hibernate.initialize(entity);``` : 프록시 강제 초기화(JPA 표준은강제 초기화 없음)


### 즉시 로딩과 지연 로딩
- ```@ManyToOne(fetch = FetchType.LAZY))``` : 지연 로딩. member 조회 시 team 조회하지 않고, team은 프록시로 가지고 있음. 실제 team을 사용하는 시점에 초기화
- ```@ManyToOne(fetch = FetchType.EAGER)``` : 즉시 로딩. member와 team을 한꺼번에 조회
- 프록시와 즉시로딩 주의
  - **가급적 지연 로딩만 사용(특히 실무에서)**
  - 즉시 로딩을 적용하면 예상하지 못한 SQL이 발생
  - **즉시 로딩은 JPQL에서 N+1 문제를 일으킴**
    - 지연 로딩 설정 후 join 필요할 땐 **fetch join** 사용
  - **@ManyToONe, @OneToOne은 기본이 즉시 로딩 -> LAZY로 설정**
  - @OneToMany, @ManyToMany는 기본이 지연 로딩
- 실무 tip
  - 모든 연관관계에 지연 로딩 사용할 것
  - JPQL fetch 조인이나 엔티티 그래프 기능 사용
  
### 영속성 전이(CASCADE)
- 영속성 전이: CASCADE
  - 특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶을 때
  - ex) 부모 엔티티를 저장할 때 자식 엔티티도 같이 저장
  - ```@OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)```
  - 종류
    - **ALL : 모두 적용(PERSIST + REMOVE)**
    - **PERSIST : 영속**
    - **REMOVE : 삭제**
    - MERGE : 병합
    - REFRESH
    - DETACH
  - 주의 사항
    - 연관관계 매핑과는 아무 관련 없음
  - 하나의 부모가 자식들을 관리할 때
    - O : 게시물 첨부파일 (단일 엔티티에 종속적일 때, 단일 소유자, 라이프 사이클이 같을 때)
    - X : 다른 객체와도 연관관계가 있는 경우

### 고아 객체
- 고아 객체 제거 : 부모 엔티티와 연관 관계가 끊어진 자식 엔티티를 자동으로 삭제
- ```@OneToMany(mappedBy = "parent", orphanRemoval = true)```
- ```java
  Parent parent1 = em.find(Parent.class, id);
  parent1.getChildren().remove(0);
  // 자식 엔티티를 컬렉션에서 제거

  --> DELETE FROM CHILD WHERE ID = ?
  ```
- 참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 보고 삭제하는 기능
- **참조하는 곳이 하나일 때 사용해야 함**
- **특정 엔티티가 개인 소유할 때 사용**
- @OneToOne, @OneToMany만 가능
- 참고 : 개념적으로 부모를 제거하면 자식은 고아가 된다. 따라서, 고아 객체 제거 기능을 활성화 하면, 부모를 제거할 때 자식도 함께 제거된다. 이것은 ```CascadeType.REMOVE``처럼 동작한다.
- ```cascade = CascadeType.ALL, orphanRemoval = true```의 의미
  - 스스로 생명주기를 관리하는 엔티티는 em.persist()로 영속화, em.remove()로 제거
  - 두 옵션을 모두 활성화 하면 부모 엔티티를 통해 자식의 생명 주기를 관리할 수 있음
  - 도메인 주도 설계(DDD)의 Aggregate Root 개념을 구현할 떄 유용
    - 자식 객체들은 Repository가 따로 필요 없음

## 값 타입
- JPA의 데이터 타입 분류
  - 엔티티 타입
    - @Entity로 정의하는 객체
    - 데이터가 변해도 식별자로 지속해서 추적 가능
    - 예) 회원 엔티티의 키나 나이 값을 변경해도 식별자로 인식 가능
  - 값 타입
    - int, Integer, String 처럼 단순히 값으로 사용하는 자바 기본 타입이나 객체
    - 식별자가 없고 값만 있으므로 변경시 추적 불가
    - 예) 숫자 100을 200으로 변경하면 완전히 다른 값으로 대체
  - 분류
    - 기본값 타입
      - 자바 기본 타입 (int, double)
      - 래퍼 클래스(Integer, Long)
      - String
    - 임베디트 타입(Embedded type, 복합값 타입)
    - 컬렉션 값 타입(Collection value type)

### 기본값 타입
- String name, int age
- 생명주기를 엔티티에 의존
  - ex) 회원 삭제 시 이름, 나이 필드도 함계 삭제
- 값 타입 공유 X
  - ex) 회원 이름 변경 시 다른 회원 이름 변경 X
- 참고
  - int, double 같은 기본 타입(primitive type)은 절대 공유 X
  - 기본 타입은 항상 값을 복사함
  - Integer 같은 래퍼 클래스나 String 같은 특수한 클래스는 공유 가능한 객체이지만 변경 X

### 임베디드 타입 (내장 타입)
![embedded_type](/Java/JPA/images/embedded_type.png)
- 새로운 값 타입을 직접 정의할 수 있음
- JPA는 임베디드 타입이라 함
- 주로 기본 값 타입을 모아서 만들어서 복합 값 타입이라고도 함
- int, String과 같은 값 타입
- 사용법
  - ```@Embeddable``` : 값 타입을 정의하는 곳에 표시
  - ```@Embedded``` : 값 타입을 사용하는 곳에 표시
  - 기본 생성자 필수
- 장점
  - 재사용
  - 높은 응집도
  - Period.isWork() 처럼 해당 값 타입만 사용하는 의미있는 메소드를 만들 수 있음
  - 임베디드 타입을 포함한 모든 값 타입은 값 타입을 소유한 엔티티에 생명주기를 의존함
  ```java
  @Embeddable
  public class Period {

    private LocalDateTime startDate;
    private LocalDateTime endDate;


  }
  ```
  ```java
  public class Member {

    ...

    @Embedded
    private Period period;

  }
  ```
- 테이블 매핑
  - 임베디드 타입은 엔티티의 값일 뿐
  - 임베디드 타입을 사용하기 전과 후에 매핑하는 테이블은 같음.
  - 객체와 테이블을 세밀하게(fine-grained) 매핑하는 것이 가능
  - 잘 설계한 ORM 애플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많음
- 한 엔티티에서 같은 값 타입 사용 -> 컬럼명 중복
  - ```@AttibuteOverrides```, ```@AttibuteOverride``` : 속성 재정의
  ```java
    @Embedded
    @AttributeOverrides({
      @AttributeOverride(name="startDate", column=@Colomn(name="WORK_START")), 
      @AttributeOverride(name="endDate", column=@Colomn(name="WORK_END"))
    })
    private Period workPeriod;
  ```
- 임베디드 타입과 null
  - 임베디드 타입이 null이면 매핑한 컬럼 값은 모두 null

### 값 타입과 불변 객체
- 값 타입 공유 참조
  - 임베디드 타입 같은 값 타입을 여러 엔티티에서 공유하면 위험함
  - 부작용(side effect) 발생
  - 값(인스턴스)을 복사해서 사용해야 함
- 객체 타입의 한계
  - 항상 값을 복사해서 사용하면 공유 참조로 인해 발생하는 부작용을 피할 수 있으나...
  - 문제는 임베디드 타입처럼 직접 정의한 값 타입은 자바의 기본 타입이 아니라 **객체 타입**
  - 자바 기본 타입에 값을 대입하면 값을 복사하지만,
  - 객체 타입은 참조 값을 직접 대입하는 것을 막을 방법이 없음
  - **객체의 공유 참조는 피할 수 없음**
- 불변 객체
  - 객체 타입을 수정할 수 없게 만들면 부작용을 원천 차단
  - 값 타입은 불변 객체로 설계해야 함
  - 불변 객체 (Immutable Object) : 생성 시점 이후 절대 값을 변경할 수 없는 개체
  - 생성자로만 값을 설정하고 수정자를 만들지 않으면 됨
  - 참고: Integer, String은 Java가 제공하는 대표적인 불변 객체

### 값 타입의 비교
- 인스턴스가 달라고 그 안에 값이 같으면 같은 것으로 봐야 함
- 동일성(identity) 비교 : 인스턴스의 참조 값을 비교, == 사용
- 동등성(equivalence) 비교 : 인스턴스의 값 비교, equals() 사용
  - equals()를 적절하게 재정의 해야함

### 값 타입 컬렉션
![type_collection](/Java/JPA/images/type_collection.png)
```java

  @ElementCollection
  @CollectionTable(name="FOOD_NAME", joinColumns=@JoinColumn(name="MEMBER_ID"))
  @Colum(name="FOOD_NAME") // 예외적 허용
  private Set<String> favoriteFoods = new HashSet();

  @ElementCollection
  @CollectionTable(name="ADRRESS", joinColumns=@JoinColumn(name="MEMBER_ID"))
  private List<Address> addressHistory = new ArrayList<>();
```
- 값 타입 하나 이상 저장할 때 사용
- ```@ElementCollection```, ```@CollectionTable``` 사용
- 데이터베이스는 컬렉션을 같은 테이블에 저장할 수 없음
- 컬렉션을 저장하기 위한 별도의 테이블 필요
- 사용
  - 저장
  - 조회
    - 지연 로딩 전략 사용
  - 수정
    - 값 타입 : 객체 생성 후 set
    - 값 타입 컬렉션
      - remove -> add
  - 참고: 영속성 전이(Cascade) + 고아 객체 제거 기능 필수로 가진다고 볼 수 있음
  - 제약사항
    - 값 타입은 엔티티와 다르게 식별자 개념이 없음
    - 값은 변경하면 추적이 어려움
    - **값 타입 컬렉션에 변경 사항이 발생하면, 주인 엔티티와 연관된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장함**
    - 값 타입 컬렉션을 매핑하는 테이블은 모든 컬럼을 묶어서 기본키를 구성해야 함: not null, unique
  - **대안**
    - 실무에서는 상황에 따라 값 타입 컬렉션 대신 **일대다 관계 사용** 고려
    - 일대다 관계를 위한 엔티티를 만들고, 여기에서 값 타입 사용
    - 영속성 전이(Cascade) + 고아 객체 제거를 사용해 값 타입 컬렉션 처럼 사용
      ```java
        @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
        @JoinColumn(name = "MEMBER_ID")
        private List<AddressEntity> addressHistory = new ArrayList<>();
      ```

### 정리
- 엔티티 타입의 특징
  - 식별자 O
  - 생명 주기 관리
  - 공유
- 값 타입 특징
  - 식별자 X
  - 생명 주기 엔티티에 의존
  - 공유하지 않는 것이 안전(복사해서 사용)
  - 불변 객체로 만드는 것이 안전
- 값 타입은 정말 값 타입이라 판단될 때만 사용할 것
- **식별자가 필요하고, 지속해서 값을 추적, 변경해야 한다면 엔티티**
  
## 객체지향 쿼리 언어 - 기본
### 기본 문법과 쿼리 API
- JPA가 지원하는 쿼리
  - **JPQL**
    - 가장 단순한 조회 방법
      - EntityManager.find()
      - 객체 그래프 탐색(a.getB().getC())
    - 나이가 18살 이상인 회원을 모두 검색하고 싶다면?
    - JPA를 사용하면 엔티티 객체를 중심으로 개발
    - 문제는 검색 쿼리
    - 모든 DB 데이터를 객체로 변환해서 검색하는 건 불가능
    - 어플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검색 조건 포함된 SQL 필요
    - JPA는 SQL 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공
      - SQL과 유사한 문법 : SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
    - SQL을 추상화해서 특정 DB SQL에 의존 X
    - JPQL은 **엔티티 객체를 대상으로 쿼리**
    ```java
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("persistence.name");
        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try {
           
            List<Member> result = em.createQuery(
              "select m From Member m where m.username like '%kim%'", Member.calss
            ).getResultList();

            tx.commit();
        } catch (Exception e) {
            tx.rollback();
            e.printStackTrace();
        } finally {
            em.close();
        }
        emf.close();
    ```
  - JPA Criteria
    - 문자가 아닌 자바코드로 JPQL 작성 가능
    - JPQL 빌더 역할
    - JPA 공식 기능
    - 동적 쿼리 짜기 좋음
    - 오타 -> 컴파일 에러
    - 복잡스러움 -> 유지보수 어려움
  - **QueryDSL**
    - 문자가 아닌 자바코드로 JPQL 작성 가능
    - JPQL 빌더 역할
    - 컴파일 시점에 문법 오류를 찾을 수 있음
    - 동적 쿼리 작성 편리
    - 단순, 쉬움
    - 실무 사용 권장
  - 네이티브 SQL
    ```java
      em.createNativeQuery("SELECT MEMBER_ID FROM MEMEBER").getResultList();
    ```
  - JDBC API 직접 사용, MyBatis, SpringJdbcTemplate과 함께 사용
    - 영속성 컨텍스트를 적절한 시점에 강제로 플러시 필요

### 프로젝션(SELECT)
- SELECT 절에 조회 할 대상을 지정하는 것
- 프로젝션 대상 : 엔티티, 임베디드 타입, 스칼라 타입(숫자, 문자 등 기본 데이터 타입)
  - ```SELECT m FROM Member m``` -> 엔티티 프로젝션
  - ```SELECT m.team FROM Member m``` -> 엔티티 프로젝션
  - ```SELECT m.address FROM Member m``` -> 임베디드 타입 프로젝션
  - ```SELECT m.username, m.age FROM Member m``` -> 스칼라 타입 프로젝션
- DISTINCT로 중복 제거
- 여러 값 조회
  - ```SELECT m.username, m.age FROM Member m```
    1. Query 타입으로 조회
      - TypeQuery : 반환 타입 명확할 때
        ```java
          TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class);
        ```
    2. Object[] 타입으로 조회
      - Query : 반환 타입 명확하지 않을 때 
        ```java
          List<Object[]> result = em.createQuery("SELECT m.username, m.age FROM Member m");
        ```
    3. new 명령어로 조회
      - 단순 값을 DTO로 바로 조회
      - 패키지 명을 포함한 전체 클래스명 입력
      - 순서와 타입이 일치하는 생성자 필요
        ```java
          em.createQuery("SELECT new x.x.x.UserDTO(m.username, m.age) FROM Member m", MemberDTO.class);
        ```

### 페이징
- JPA는 페이징을 다음 두 API로 추상화
  - ```setFirstResult(int startPosition)``` : 조회 시작 위치(0부터 시작)
  - ```setMaxResult(int maxResult)``` : 조회할 데이터 수

### 조인
- 종류
  - 내부 조인 (INNER JOIN)
    - ```SELECT m FROM Member m [INNER] JOIN m.team t```
  - 외부 조인 (OUTER JOIN)
    - ```SELECT m FROM Member m LEFT [OUTER] JOIN m.team t```
  - 세타 조인 (CROSS JOIN)
    - ```select count(m) from Member m, Team t where m.username = t.name```
- 조인 - ON절 (JPA 2.1~)
  1. 조인 대상 필터링
       - ```SELECT m, t FROM Member m LEFT JOIN m.team t ON t.name = 'A'``` : 회원과 팀을 JOIN 하면서, 팀 이름이 A인 팀만 JOIN
  2. 연관관계 없는 엔티티 외부 조인 (Hibernate 5.1~) 
       - ```SELECT m, t FROM Member m LEFT JOIN Team t ON m.username = t.name``` : 회원의 이름과 팀의 이름이 같은 대상 외부 조인 

### 서브쿼리
- 나이가 평균 보다 많은 회원
  - ```SELECT m FROM Member m WHERE m.age > (SELECT AVG(m2.age) FROM Member m2)```
- 한 건이라도 주문한 고객
  - ```SELECT m FROM Member m WHERE (SELECT COUNT(o) FROM Order o WHERE m = o.member) > 0```
- 서브 쿼리 지원 함수
  - [NOT] EXISTS (subquery) : 서브쿼리에 결과가 존재하면 참
    - {ALL | ANY | SOME} (subquery)
    - ALL 모두 만족하면 참
    - ANY, SOME : 동일한 의미, 조건 하나라도 만족하면 참
    - 팀 A 소속인 회원 : ```SELECT m FROM Member m WHERE EXISTS (SELECT t FROM m.team t WHERE t.name = '팀A')```
    - 전체 상품 각각의 재고보다 주문량이 많은 주문들 : ```SELECT o FROM Order o WHERE o.orderAmount > ALL (SELECT p.stockAmount FROM Product p)```
    - 어떤 팀이든 팀에 소속된 회원 : ```SELECT m FROM Member m WHERE m.team = ANY (SELECT t FROM Team t)```
  - [NOT] IN (subquery) : 서브쿼리의 결과 중 하나라도 같은 것이 있으면 참
- 한계
  - WHERE, HAVING 절에서만 서브 쿼리 사용 가능
  - SELECT 절도 가능 (Hibernate에서 지원)
  - FROM 절의 서브 쿼리는 현재 JQPL에서 불가
    - JOIN으로 풀 수 있으면 풀어서 해결

### JPQL 타입 표현과 기타식
- 문자 : 'HELLO', 'She''s'
- 숫자 : 10L(Long), 10D(Double), 10F(Float)
- Boolean : TRUE, FALSE
- ENUM : package.package.ClassName (패키지명 포함)
- 엔티티 타입 : TYPE(m) = Member (상속 관계에서 사용)
- SQL과 문법 비슷
  - EXISTS, IN
  - AND, OR, NOT
  - =, >, >=, <, <=, <>
  - BETWEEN, LIKE, IS NULL

### 조건식 (CASE 등등)
- 기본 CASE식
```
SELECT
  CASE WHEN m.age <= 10 THEN '학생요금'
       WHEN a.age >= 60 THEN '경로요금'
       ELSE '일반요금'
  END
FROM Member m
```
-  단순 CASE식
```
SELECT
  CASE t.name
    WHEN '팀A' THEN '인센티브110%'
    WHEN '팀B' THEN '인센티브120%'
    ELSE '인센티브105%'
  END
FROM Team t
```
- **COALESCE** : 하나씩 조회해서 NULL이 아니면 반환
  - ```SELECT COALESCE(m.username, '이름 없는 회원') FROM Member m``` : 사용자 이름이 없으면 이름 없는 회원을 반환
- **NULLIF** : 두 값이 같으면 NULL, 다르면 첫 번째 값 반환
  - ```SELECT NULLIF(m.username, '관리자') FROM Member m``` : 사용자 이름이 '관리자'면 NULL 반환하고 나머지는 username 반환

### JPQL 함수
- CONCAT
- SUBSTRING
- TRIM
- LOWER, UPPER
- LENGTH
- LOCATE
- ABS, SQRT, MOD
- SIZE, INDEX
- 사용자 정의 함수
  - 하이버네이트는 사용 전 방언에 추가해야 함
    - 사용하는 DB 방언을 상속받고, 사용자 정의 함수를 등록
    - ```SELECT FUNCTION('group_concat', i.name) FROM Item i```
    - ```SELECT group_concat(i.name) FROM Item i``` : Hibernate 지원
    - **Dialect 생성 후, persistence.xml에 dialect 등록**
    ```java

      public class MyH2Dialect extends H2Dialect {
        public MyH2Dialect() {
          registerFunction("gropu_concat", new StandardSQLFunction("group_concat", StandardBasicTypes.STRING));
        }
      }

    ```

## 객체지향 쿼리 언어 - 중급
### 경로 표현식
- .을 찍어 객체 그래프를 탐색하는 것
```
SELECT m.username -- 상태 필드
       , m.team.name
  FROM Member m
  JOIN m.team t   -- 단일 값 연관 필드
  JOIN m.orders o -- 컬렉션 값 연관 필드
 WHERE t.name = '팀A'
```
- 용어
  - 상태 필드(state field) : 단순히 값을 저장하기 위한 필드 ex) m.username
  - 연관 필드(association field) : 연관관계를 위한 필드
    - 단일 값 연관 필드
      - ```@ManyToOne```, ```@OneToOne``` 대상이 엔티티 ex) m.team
    - 컬렉션 값 연관 필드
      - ```@OneToMany```, ```@ManyToMany``` 대상이 컬렉션 ex) m.orders
- 특징
  - 상태 필드 : 경로 탐색의 끝, 탐색 X
  - 단일 값 연관 경로 : **묵시적 내부 조인** 발생, 탐색 O
  - 컬렉션 값 연관 경로 : 묵시적 내부 조인 발생, 탐색 X
    - FROM 절에서 명시적 조인을 통해 별칭을 얻으면 별칭을 통해 탐색 가능
  - 묵시적 내부 조인 -> 쿼리 튜닝 어려움. 실무에서는 사용 지양
- JOIN
  - 명시적 조인 : join 키워드 직접 사용
    ```select m from Member m join m.team t```
  - 묵시적 조인 : 경로 표현식에 의해 묵시적으로 SQL 조인 발생(내부 조인만 가능)
    ```select m.team from Member m```
- 경로 탐색을 사용한 묵시적 조인 시 주의 사항
  - 항상 내부 조인
  - 컬렉션은 경로 탐색의 끝, 명시적 조인을 통해 별칭을 얻어야 함
  - 경로 탐색은 주로 SELECT, WHERE 절에서 사용하지만 묵시적 조인으로 인해 SQL의 FROM(JOIN) 절에 영향을 줌 
- 정리
  - **가급적 묵시적 조인 대신 명시적 조인 사용**
  - 조인은 SQL 튜닝에 중요 포인트
  - 묵시적 조인은 조인이 일어나는 상황을 한눈에 파악하기 어려움

### 페치 조인
- SQL 조인 종류 X
- JPQL에서 **성능 최적화**를 위해 제공하는 기능
- 연관된 엔티티나 컬렉션을 SQL **한 번**에 함께 조회하는 기능
- ```join fetch```

#### Entity Fetch Join
- 회원을 조회하면서 연관된 팀도 함께 조회
  - JPQL
    ```
    select m from Member m join fetch m.team
    ```
  - SQL
    ```
    SELECT M.*, T.* FROM MEMBER M INNER JOIN TEAM R ON M.TEAM_ID = T.ID
    ```

#### Collection Fetch Join
- 일대다 관계
  - JPQL
    ```
    select t
      from Team
      join fetch t.members
     where t.name = '팀A'
    ```
  - SQL
    ```
    SELECT T.*, M.*
      FROM TEAM T
     INNER JOIN MEMBER M
        ON T.ID = M.TEAM_ID
     WHERE T.NAME = '팀A'
    ```

#### JPQL의 DISTINCT
```
select distinct t 
  from Team t
  join fetch t.members
  where t.name = '팀A'
```
1. SQL에 DISTINCT 추가
   - SQL에 DISTINCT 추가해도 데이터가 달라 SQL 결과에서 중복 제거 X
2. **어플리케이션에서 엔티티 중복 제거**
   -  DISTINCT가 추가로 애플리케이션에서 중복 제거 시도
   -  같은 식별자를 가진 엔티티 제거 

#### 페치 조인과 일반 조인의 차이
- JPQL은 결과 반환 시 연관관계 고려 X
- SELECT절에 지정한 엔티티만 조회할 뿐
- 페치 조인을 사용할 때만 연관된 엔티티도 함께 조회(즉시 로딩)
- ***페치 조인은 객체 그래프를 SQL 한 번에 조회하는 개념**

#### 페치 조인의 특징과 한계
- **페치 조인 대상에는 별칭을 줄 수 없음**
  - 하이버네이트는 가능, 가급적 사용 X
- 둘 이상의 컬렉션은 페치 조인 할 수 없음
- 컬렉션을 페치 조인하면 페이징 API(setFirstResult, setMaxResults) 사용할 수 없음
  - 일대일, 다대일 같은 단일 값 연관 필드들은 페치 조인 해도 페이징 가능
  - 하이버네이트는 경고 로그를 남기고 메모리에서 페이징(매우 위험)
- **Batch Size**  : N + 1 해결
  - 엔티티에 설정 (1000이하)
    ```
      @BatchSize(size = 100)
      @OneToMany(mappedBy = "team")
      private List<Member> members = new ArrayList<>();
    ```
  - 글로벌 세팅도 가능 (1000이하)
- 연관된 엔티티들을 SQL 한 번으로 조회 - 성능 최적화
- 엔티티에 직접 적용하는 글로벌 로딩 전략보다 우선
- 실무에서 글로벌 로딩 전략은 모두 지연 로딩
- 최적화가 필요한 곳에 페치 조인 적용
- 정리
  - 모든 것을 페치 조인으로 해결 X
  - 페치 조인은 객체 그래프 유지할 떄 사용하면 효과적
  - 여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 하면, 페치 조인 보다는 일반 조인을 사용하고 필요한 데이터들만 조회해서 DTO로 반환하는 것이 효과적

### 다형성 쿼리
- TYPE
  - 조회 대상을 특정 자식으로 한정
  - ex) Item 중 Book, Movie 조회
    ```
    [ JPQL ]
    select i from Item i where type(i) IN (Book, Movie)

    [ SQL ]
    select i from i
    where i.DTYPE in ('B', 'M')
    ```
- TREAT (JPA2.1)
  - 자바의 타입 캐스팅과 유사
  - 상속 구조에서 부모 타입을 특정 자식 타입으로 다룰 때 사용
  - FROM, WHERE, SELECT(하이버네이트 지원) 사용
  - ex) 부모인 Item과 자식 Book이 있다
    ```
    [ JPQL ]
    select i from Item i
    where treat(i as Book).author = 'kim'

    [ SQL ]
    select i.* from Item i 
    where i.DTYPE = 'B' and i.author = 'kim' 
    ```

### 엔티티 직접 사용
- 기본키 값
  - JPQL에서 엔티티를 직접 사용하면 SQL에서 해당 엔티티의 기본키 값을 사용
    ```
    [ JPQL ]
    select count(m.id) from Member m -- 엔티티의 아이디 사용
    select count(m) from Member m -- 엔티티 직접 사용

    [ SQL ]
    select count(m.id) as cnt from Member m
    ```

### Named 쿼리

### 벌크 연산