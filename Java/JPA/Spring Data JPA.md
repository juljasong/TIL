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

## 순수 JPA 페이징과 정렬

