# 공통 인터페이스
## 제네릭 타입
- T : 엔티티
- ID : 엔티티의 식별자 타입
- S : 엔티티와 그 자식 타입

## 주요 메서드
- ```save(S)``` : 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합한다.
- ```delete(T)``` : 엔티티 하나를 삭제한다. 내부에서 EntityManager.remove() 호출
- ```findById(ID)``` : 엔티티 하나를 조회한다. 내부에서 EntityManager.find() 호출
- ```getOne(ID)``` : 엔티티를 프록시로 조회한다. 내부에서 EntityManager.getReference() 호출
- ```findAll(…)``` : 모든 엔티티를 조회한다. 정렬( Sort )이나 페이징( Pageable ) 조건을 파라미터로 제공할 수
있다.

# 쿼리 메소드

## 메소드 이름으로 쿼리 생성
- [JPA Query Method doc](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)
  - 조회: find…By ,read…By ,query…By get…By
  - COUNT: count…By 반환타입 long
  - EXISTS: exists…By 반환타입 boolean
  - 삭제: delete…By, remove…By 반환타입 long
  - DISTINCT: findDistinct, findMemberDistinctBy
  - [LIMIT](https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#repositories.limit-query-result): findFirst3, findFirst, findTop, findTop3

## @Query, 리포지토리 메소드에 쿼리 정의하기
```java
// 정의
@Query("select m from Member m where m.username = :username and m.age = :age")
List<Member> findUser(@Param("username") String username, @Param("age") int age);

// 사용
memberRepository.findUser("member", 10);
```
- 장점
  - jpql(정적 쿼리)을 간단하게 쓸 수 있음
  - 어플리케이션 로딩 시점에 SQL 문법 오류를 잡아줌

### NamedQuery (@Query가 있어서 잘 안씀....)
- 쿼리 검색 순서 : NamedQuery -> Query Method
```java
// 정의
@NamedQuery(name = "memberRepository.findByAge", query = "select m from Member m where age = :age")
public interface MemberRepository extends JpaRepository<Member, Long> {

    List<Member> findByUsernameAndAgeGreaterThan(String username, int age);

    @Query(name = "Member.findByAge")
    List<Member> findByAge(@Param("age") int age);
}

// 사용
memberRepository.findByAge(10);

```
- 장점
  - 어플리케이션 로딩 시점에 SQL 문법 오류를 잡아줌

## @Query, 값, DTO 조회
### 값
```java
@Query("select m.username from Member m where m.username = :username and m.age = :age")
List<String> findUsername(@Param("username") String username, @Param("age") int age);
```

### DTO
```java
@Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) from Member m join m.team t")
List<MemberDto> findMemberDto();
```

## 파라미터 바인딩
- 위치 기반
- **이름 기반**
- 컬렉션 
```java
// 정의
@Query("select m from Member m where m.username in :names")
List<Member> findByNames(@Param("names")List<String> names);

// 사용
List<Member> members = memberRepository.findByNames(Arrays.asList("memberA", "memberB"));
```

## 반환타입
- Collection<T>
- T
- Primitive
- Wrapper
- Optional
- ...

## 페이징과 정렬
### 순수 JPA
```java
public List<Member> findByPage(int age, int offset, int limit) {
    return em.createQuery("select m from Member m where m.age = :age order by m.username desc", Member.class)
            .setParameter("age", age)
            .setFirstResult(offset) // offset 1부터 시작
            .setMaxResults(limit)
            .getResultList();
}

public long totalCount(int age) {
    return em.createQuery("select count(m) from Member m where m.age = :age", Long.class)
            .setParameter("age", age)
            .getSingleResult();
}
```

### Spring Data JPA
- Page
```java
// 정의
@Query(value = "select m from Member m left join m.team t", 
       countQuery = "select count(m) from Member m") // count 쿼리 분리 가능
Page<Member> findByAge(int age, Pageable pageable);

// 사용
int age = 10;
PageRequest pageRequest = PageRequest.of(0, 3, Sort.Direction.DESC, "username");
// offset 0부터 시작

Page<Member> page = memberRepository.findByAge(10, pageRequest);

List<Member> members = page.getContent();
int totalPages = page.getTotalPages();    // 전체 페이지 수
int size = page.getSize();                // 현재 페이지 사이즈
int number = page.getNumber();            // 현재 몇 번째
boolean hasNext = page.hasNext();         // 다음 페이지 존재 여부
```
- Slice
```java
Slice<Member> findByAge(int age, Pageable pageable);

Slice<Member> slice = memberRepository.findByAge(10, pageRequest);

List<Member> members = slice.getContent();
int size = slice.getSize();
int number = slice.getNumber();
boolean hasNext = slice.hasNext();
```

## 벌크성 수정 쿼리
```java
@Modifying(clearAutomatically = true) // executeUpdate()
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```
- 벌크 연산은 영속성 컨텍스트 관리 X
- 벌크 연산 후에는 영속성 컨텍스트 날려야 함
  - ```em.flush();``` : DB 반영
  - ```em.clear();``` : 영속성 컨텍스트 날리기
  - or
  - ```@Modifying(clearAutomatically = true)```

## @EntityGraph
- fetch join
```java
@Query("select m from Member m left join fetch m.team t")
List<Member> findMemberFetchJoin();
```
- EntityGraph
```java
@Override
@EntityGraph(attributePaths = {"team"}) // fetch 조인됨 
List<Member> findAll();
```

## JPA Hint
- 순수 조회용으로 find 하는 경우 ```@QueryHints```
- 영속성 컨텍스트 관리 X
- 굳이 쓸 필요 없으면 안씀..
```java
@QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
Member findReadOnlyByUsername(String username);
```

## JPA Lock
```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
List<Member> findLockByUsername(String username);
```
=>
```
select m1_0.member_id,m1_0.age,m1_0.team_id,m1_0.username from member m1_0 where m1_0.username=? for update
```
- [for update](https://dololak.tistory.com/446)
- 실시간 트래픽이 많은 경우에는 사용을 지양하는 것이 좋음..

# 확장 기능
## 사용자 정의 리포지토리 구현
- Interface로 해결이 안되는 경우 QueryDsl 등을 사용해야 할 때 커스텀 필요
  - Interface :: 정의   
  - XXXRepository**Impl** extends [interface] :: 인터페이스 구현
  - XXXRepository implements JpaRepository<XXX, Long>, [interface] :: JpaRepository와 연동
- **항상 사용자 정의 리포지토리가 필요한 것이 아님.** 임의의 리포지토리 만들어 빈으로 등록해서 그냥 직접 사용해도 됨.


## Auditing
- 엔티티 생성, 변경한 사람과 시간을 추적하기 위한 기능
  - 등록일
  - 수정일
  - 등록자
  - 수정자
- 순수 JPA 주요 어노테이션
  - ```@MappedSuperclass```
  - ```@PrePersist```,```@PreUpdate```
  - ```@PostPersist```,```@PostUpdate```
- Spring Data JPA
  - ```@EnableJpaAuditing```
  - ```@CreatedDate```, ```@LastModifiedDate```
  - ```@CreatedBy```,```@LastModifiedBy```
```java
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseEntity {

    // 일시만 따로 관리하는 엔티티를 만들어서 상속받아 사용하는 것도 방법
    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;

    @CreatedBy
    @Column(updatable = false)
    private String createdBy;

    @LastModifiedBy
    private String lastModifiedBy;

}
```
```java
@EnableJpaAuditing // <-
@SpringBootApplication
public class DataJpaApplication {

	public static void main(String[] args) {
		SpringApplication.run(DataJpaApplication.class, args);
	}

	@Bean
	public AuditorAware<String> auditorProvider() { // <-
		return () -> Optional.of(UUID.randomUUID().toString()); // 수정자
	}

}
```

## Web 확장 - 도메인 클래스 컨버터
- pk 조회 시에만 사용 가능

## Web 확장 - 페이징과 정렬
```java
@GetMapping("/members")
public Page<Member> list(@PageableDefault(size = 5)Pageable pageable) {
    return memberRepository.findAll(pageable);
}
```
```url
http://localhost:8080/members?page=1&size=10&sort=id,desc
```
- page : 0부터 시작
- size : 크기
- sort : 디폴트 asc
- 글로벌 기본 값 변경은 application.yml
    ```yml  
    spring:
      data:
        web:
          pageable:
            default-page-size: 10
            max-page-size: 100
    ```
 - 개별 설정 ```@PageableDefault```
 - 접두사
   - 페이징 정보가 둘 이상일 때 ```@Qaulifier("xxx")```

# 스프링 데이터 JPA 분석
## 새로운 엔티티를 구별하는 방법
- 식별자가 객체 :: null
- 식별자가 자바 기본 타입 :: 0
- `Persistable` 인터페이스 구현 -> isNew() 오버라이드

# 나머지 기능들
## Specifications(명세)
- 술어(predicate)
  - 참 또는 거짓으로 평가
  - ANd OR 같은 연산자로 조합하여 다양한 검색조건을 쉽게 생성(컴포지트 패턴)
- Criteria 활용하여 사용할 수 있음
- QueryDSL 사용하자

## Query By Example
- 장점
  - 도메인 객체를 그대로 사용
  - 동적 쿼리를 편리하게 처리
  - RDB에서 NOSQL로 바꿔도 코드 변경 없이 사용 가능
  - 스프링 데이터 JPA 인터페이스에 이미 포함
- 단점
  - INNER JOIN만 가능
  - 중첩 제약 조건 불가
  - 단순한 매칭 조건
- QueryDSL 사용하자

## Projections
- 엔티티 대신 DTO 조회할 때 사용
- 전체 엔티티가 아니라 특정 값만 조회할 때
- 인터페이스 기반
  1. 인터페이스 정의
      ```java
      @Value("#{target.username + ' ' + target.age}") // 문자열 붙이기 가능. 필요 시에만
      public interface UsernameOnly {
        String getUsername();
      }
      ```
  2. JPA 리포지토리에서 Return 타입으로 구현
      ```java
      List<UsernameOnly> findProjectionsByUsername(@Param("username")String username);
      ```
  3. 사용
      ```java
      List<UsernameOnly> members = memberRepository.findProjectionsByUsername("memberA");
      ```
- 클래스 기반
  1. 클래스 정의
      ```java
      @Getter
      public class UsernameOnlyDto {
          private final String username;

          public UsernameOnlyDto(String username) {
              this.username = username;
          }
      }
      ```
  2. JPA 리포지토리에서 Return 타입으로 구현
      ```java
      List<UsernameOnlyDto> findProjectionsByUsername(@Param("username")String username);
      ```
  3. 사용
      ```java
      List<UsernameOnly> members = memberRepository.findProjectionsByUsername("memberA");
      ```
- 중첩 구조
    ```java
    public interface NestedClosedProjections {
      String getUsername();
      TeamInfo getTeam();

      interface TeamInfo {
          String getName();
      }
    }
    ```
    - ```select m.username,t.team_id,t.name from member m left join team t on t.team_id=m.team_id where m.username=?```
    - 프로젝션 대상이 root 엔티티면, JPQL SELECT절 최적화 가능
    - 프로젝션 대상이 ROOT가 아니면
      - LEFT OUTER JOIN 처리
      - 모든 필드를 SELECT 해서 엔티티로 조회한 다음 계산
    - 정리
      - 프로젝션 대상이 ROOT 엔티티일 때 유용
      - 복잡한 쿼리 해결에는 한계가 있음

## 네이티브 쿼리 
```java
@Query(value = "select * from member where username = ?", nativeQuery = true)
Member findByNativeQuery(String username);
```
- 사용 권장 X -> Projection 활용
  - 반환타입: Object[], Tuple, DTO => 부족
  - 제약
    - Sort 파라미터를 통한 정렬이 정상 동작 하지 않을 수 있음
    - 어플리케이션 로딩 시점 문법 확인 불가
    - 동적 쿼리 불가

### Projection 활용
- Native Query + Projection
```java
public interface MemberProjection {
    Long getId();
    String getUsername();
    String getTeamName();
}
```
```java
@Query(value = "select m.member_id as id, m.username, t.name as teamName " +
                "from member m left join team t",
                countQuery = "select count(*) from member", nativeQuery = true)
Page<MemberProjection> findByNativeProjection(Pageable pageable);
```
```java
Page<MemberProjection> memberList = memberRepository.findByNativeProjection(PageRequest.of(0, 10));
List<MemberProjection> contents = memberList.getContent();
```

### 동적 네이티브 쿼리
```java
String sql = "select m.username as username from member m";
List<MemberDto> result = em.createNativeQuery(sql)
                            .setFirstResult(0)
                            .setMaxResults(10)
                            .unwrap(NativeQuery.class)
                            .addScalar("username")
                            .setResultTransformer(Transformers.aliasToBean(MemberDto.class))
                            .getResultList();
                          
```
- 하이버네이트 활용
- Spring JdbcTemplate, myBatis, jooq 같은 외부 라이브러리 사용하는게 나음