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