# 기본 문법
## 기본 Q-Type 활용
```java
QMember m = new QMember(m);
QMember m = QMember.member;
```

## 검색 조건 쿼리
- JPQL이 제공하는 모든 검색 조건 제공
```java
member.username.eq("member1")       // username = 'member1'
member.username.ne("member1")       //username != 'member1'
member.username.eq("member1").not() // username != 'member1'
member.username.isNotNull()         //이름이 is not null
member.age.in(10, 20)               // age in (10,20)
member.age.notIn(10, 20)            // age not in (10, 20)
member.age.between(10,30)           //between 10, 30
member.age.goe(30)                  // age >= 30
member.age.gt(30)                   // age > 30
member.age.loe(30)                  // age <= 30
member.age.lt(30)                   // age < 30
member.username.like("member%")     //like 검색
member.username.contains("member")  // like ‘%member%’ 검색
member.username.startsWith("member") //like ‘member%’ 검.색
```
- and의 두 가지 방법
```java
.where(
        member.username.eq("memberA")
        .and(member.age.eq(10))
)
```
```java
.where(
        member.username.eq("memberA"),
        member.age.eq(10)
)
```

## 결과 조회
- ```fetch()``` : 리스트 조회, 데이터 없으면 빈 리스트 반환
- ```fetchOne()``` : 단 건 조회
  - 결과가 없으면 : null
  - 결과가 둘 이상이면 : com.querydsl.core.NonUniqueResultException
- ```fetchFirst()``` : limit(1).fetchOne()
- ~~```fetchResults()```~~ : 페이징 정보 포함, total count 쿼리 추가 실행
- ~~```fetchCount()```~~ : count 쿼리로 변경해서 count 수 조회

## 정렬
```java
.orderBy(
        member.age.desc(),
        member.age.asc().nullsLast()
)
```
- ```desc()``` , ```asc()``` : 일반 정렬
- ```nullsLast()``` , ```nullsFirst()``` : null 데이터 순서 부여

## 페이징
- ```offset()``` : 시작 지점(0)
- ```limit()``` : 개수

## 집합
- 집합 함수
```java
List<Tuple> result = queryFactory
    .select(
        member.count(),     // 수
        member.age.sum(),   // 합계
        member.age.avg(),   // 평균
        member.age.max(),   // 최대
        member.age.min())   // 최소
    .from(member)
    .fetch();
```
- GroupBy
```java
 …
.groupBy(item.price)
.having(item.price.gt(1000))
 …
```
  - ```groupBy()``` : 그룹화
  - ```having()``` : 그룹화된 결과 제한

## 조인
### 기본 조인
```java
 …
.join(member.team, team)
 …
```
- ```join()``` , ```innerJoin()``` : 내부 조인(inner join)
- ```leftJoin()``` : left 외부 조인(left outer join)
- ```rightJoin()``` : rigth 외부 조인(rigth outer join)
- theta join(막조인) : 연관관계 없는 컬럼으로 조인
    ```java
    List<Member> memberList = queryFactory
                .selectFrom(member)
                .from(member, team)
                .where(member.username.eq(team.name))
                .fetch();
    ```

### ON절
- 조인 대상 필터링 -> where로 치환 가능
    ```java
    List<Tuple> result = queryFactory
                    .select(member, team)
                    .from(member)
                    .leftJoin(member.team, team)
                    .on(team.name.eq("teamA"))
                    //.where(team.name.eq("teamA"))
                    .fetch();
    ```
- 연관관계 없는 엔티티 외부 조인
    ```java
    List<Tuple> memberList = queryFactory
                .select(member, team)
                .from(member)
                .join(team)
                .on(member.username.eq(team.name))
                .fetch();
    ```

### 페치 조인
- SQL조인을 활용해서 연관된 엔티티를 SQL 한번에 조회
- 주로 성능 최적화에 사용
```java
Member member1 = queryFactory
                .selectFrom(member)
                .join(member.team, team).fetchJoin()
                .where(member.username.eq("memberA"))
                .fetchOne();
```

## 서브 쿼리
```java
List<Member> result = queryFactory
                .selectFrom(member)
                .where(member.age.eq(
                        JPAExpressions  // static import 가능
                                .select(memberSub.age.max())
                                .from(memberSub)
                ))
                .fetch();
```
- ```com.querydsl.jpa.JPAExpressions``` 사용
- select, where 절에서 사용 가능
- 한계
  - from 절의 서브쿼리(인라인 뷰) 지원 X
  - 해결 방안
    1. 서브쿼리를 join으로 변경
    2. 애플리케이션에서 쿼리를 2번 분리해서 실행
    3. nativeSQL 사용

## Case문
- select, 조건절(where), order by에서 사용 가능
- 단순한 조건
    ```java
    List<String> result = queryFactory
                    .select(member.age
                            .when(10).then("열살")
                            .when(20).then("스무살")
                            .otherwise("기타")
                            )
                    .from(member)
                    .fetch();
    ```
- 복잡한 조건
    ```java
    List<String> result = queryFactory
                    .select(new CaseBuilder()
                                .when(member.age.between(0, 20)).then("0~20살")
                                .when(member.age.between(21, 30)).then("21~30살")
                                .otherwise("기타"))
                    .from(member)
                    .fetch();
    ```

## 상수, 문자 더하기
### 상수
- Expressions.constant("xxx") 사용

### 문자 더하기
- ```concat()```

# 중급 문법