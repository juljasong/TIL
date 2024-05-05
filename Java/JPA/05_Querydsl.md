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
## 프로젝션과 결과 반환
### 기본
- 프로젝션 : select 대상 지정
- 프로젝션 대상이 하나
  - 프로젝션 대상이 하나면 타입을 명확하게 지정할 수 있음
  - 프로젝션 대상이 둘 이상이면 튜플이나 DTO로 조회
- 튜플 조회
  - 프로젝션 대상이 둘 이상일 때 사용
  - repository나 dao 밑 계층(controller, service)에서는 사용 지양

### DTO 조회
- 순수 JPA에서 DTO 조회
  - new 명령어를 사용해야함
    ```
    em.createQuery("select new study.querydsl.dto.MemberDto(m.username, m.age) from Member m", MemberDto.class)
    ```
- Querydsl Bean 생성(population)
  - setter 활용
    ``` 
    queryFactory.select(Projections.bean(MemberDto.class, member.username, member.age))
                .from(member)
    ```
  - field 활용 (getter/setter 없어도 됨)
    ``` 
    queryFactory.select(Projections.fileds(MemberDto.class, member.username, member.age))
                .from(member)
    ```  
  - 생성자 활용
    ``` 
    queryFactory.select(Projections.constructor(MemberDto.class, member.username, member.age))
                .from(member)
    ```
  - 필드명 안맞을 때
    - ```member.username.as("name")``` : as("xxx")에 필드명 넣어주면 매칭됨
    - ExpressionUtils 사용
        ```java
        ExpressionUtils.as(JPAExpressions
                                .select(member.age.max())
                                .from(memberSub), "age")
        ```

### @QueryProjection
- 생성자에 ```@QueryProjection``` 어노테이션 추가 후 아래와 같이 사용
    ```java
    queryFactory
        .select(new QMemberDto(member.username, member.age))
        .from(member)
        .fetch();
    ```
    - 컴파일러로 타입을 체크할 수 있으므로 가장 안전한 방법
    - DTO에 QueryDSL 어노테이션을 유지해야 하는 점과 DTO까지 Q파일을 생성해야 함..

## 동적 쿼리
### BooleanBuilder 사용
```java
    BooleanBuilder builder = new BooleanBuilder(); // 초기값 세팅도 가능
        if (usernameCond != null)
            builder.and(member.username.eq(usernameCond));

        if (ageCond != null)
            builder.and(member.age.eq(ageCond));

        return queryFactory.selectFrom(member).where(builder).fetch();
```

### Where 다중 파라미터 사용
```java
    return queryFactory
                .selectFrom(member)
                .where(usernameEq(usernameCond), ageEq(ageCond))
                // where 절에 null이 들어오면 알아서 무시됨
                .fetch();   
```
```java
    private Predicate usernameEq(String usernameCond) {
        return usernameCond != null ? member.username.eq(usernameCond) : null;
    }
``` 
- BooleanExpression을 이용해서 조립 가능
```java
    private BooleanExpression allEq(String usernameCond, Integer ageCond) {
        return usernameEq(usernameCond).and(ageEq(ageCond));
    }
```

## 수정, 삭제 벌크 연산
- 수정
    ```java
        queryFactory
                .update(member)
                .set(member.username, "비회원")
                .where(member.age.lt(28))
                .execute();
    ```
    - ```set(member.age, member.age.add(1))``` : 더하기
    - ```set(member.age, member.age.multiply(2))``` : 곱하기
- 삭제
    ```java
        queryFactory
                .delete(member)
                .where(member.age.gt(18))
                .execute();
    ```

## SQL function 호출하기
```java
    queryFactory
            .select(Expressions.stringTemplate(
                    "function('replace', {0}, {1}, {2})",
                    member.username, "member", "M"))
            .from(member)
            .fetch();
```
```java
    queryFactory
            .select(member.username)
            .from(member)
//          .where(member.username.eq(
//                 Expressions.stringTemplate("function('lower', {0})", member.username)))
            .where(member.username.eq(member.username.lower()))
            .fetch();
```
- 커스텀 함수를 쓰려면 Dialect에 명시해야 함