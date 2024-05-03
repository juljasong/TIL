```java
extends JpaRepository<Object, Long>
```

## JpaRepositoty 계층구조
- ```Repository<\<Interface>>```
  - ```CrudRepository<\<Interface>>```
    - save()
    - findOne()
    - exist()
    - count()
    - delete()
    - ```PagingAndSortingRepository<\<Interface>>```
      - findAll(Sort)
      - findAll(Pageable)
      - ```JpaRepository<\<Interface>>```
        - findAll() : List\<T>
        - findAll(Sort)
        - findAll(Iterable\<ID>)
        - save(Iterable\<S>)
        - saveAndFlush()
  
## JPA 관련 Annotation
- @Modifying
  - 벌크 연산 시 캐시 문제 해결
- @Query
  - 벌크 연산 시 사용.  
- @Repository
- @Enumetrated
  - ENUM 타입 매핑
- @Builder
- <\<Table Class>>
- @Data
- @Entity
- @Table
- @Id
- @GeneratedValue
- @Configuration
- @PersistenceContext
- @Bean
- @Getter
- @Transactional
- @NoArgsConstructor
  - 기본 생성자 생성
- @Builder
- @EnableJpaRepositories

## Spring Data JPA에서 데이터 조회하기
### Query Method
```java
    // select u from Review u where u.title = ?
    List<Review> findByTitle(String title);
```
- 예약어
  - Distinct
  - And
  - Or
  - Is, Equals
  - Between
  - LessThan
  - LessThanEqual
  - GreaterThan
  - GreaterThanEqual
  - After
  - Befor
  - IsNull, Null
  - IsNotNull, NotNull
  - Like
  - NotLike
  - StartingWith
  - EndingWith
  - Containing
  - OrderBy
  - Not
  - In
  - NotIn
  - True
  - False
  - IgnoreCase

### JPQL
- 기본 문법
  - String jpql = "select c from review c";
  - List\<Review> result = em.createQuery(jpql, Reviwe.class).getResultList();
  - 대소문자 구분
  - 엔티티 이름(설정 명칭)
  - 별칭 필수
- TypedQuery
    ```TypedQuery<Review> query = em.createQuery(jpql, Review. class);```
- Query
    ```Query<Object> query = em.createQuery(jpql, Review.class)```

### Criteria

### Querydsl
- JPQL의 문제를 보완한 오픈소스
  - 문자열인 쿼리는 문법 오류를 발견할 수 없음
- 장점
  - 컴파일 시점에 오류 발견
  - 코드는 JPQL과 유사
  - 동적 쿼리
- SELECT
```
    // select m from Review m where cnt > 10

    JPAFactoryQuery query = new JPAFactoryQuery(em);
    QReview m = QReview.review;

    List<Review> list = query.selectFrom(m)
                             .where(m.cnt.gt(10))
                             .orderBy(m.cnt.desc())
                             .fetch();
```
- JOIN
```
    JPAFactoryQuery query = new JPAFactoryQuery(em);
    QReview m = QReview.review;
    QUser t = Quser.user;

    List<Review> list = query.selectFrom(m)
                             .join(m.userId, t)
                             .where(t.name.eq("chicken"))
                             .fetch();
```
- Paging
```
    JPAFactoryQuery query = new JPAFactoryQuery(em);
    QReview m = QReview.review;
    
    List<Review> list = 
        query.selectFrom(m)
             .where(m.count.gt(10))
             .orderBy(m.count.desc())
             .limit(10)
             .offset(10)
             .fetch();
```

##  JPA에서 성능 최적화에 대한 고민
1. 느린 쿼리 개선
2. Fetch 타입의 정확한 선택
   - 매핑 관계에 있어 잘못된 FetchType을 선택하게 되면 수행되는 쿼리 수 늘어남
   - 기본적으로 DB 튜닝에 있어 쿼리의 숫자를 줄이는 방식이랑 동일한 개념
3. JOIN 대신 FETCH JOIN 사용하기
   - 조인은 두가지 테이블 읽기 떄문에 성능 상 느려짐
   - 따라서 특정 영역 데이터 먼저 가져와 조인하는 방식이 유리
4. 데이터 베이스가 어려운 일 작업하게 하기
   - 간혹 서비스를 구현함에 로직 및 구현 부분에서 데이터를 가져와 처리하는 경우가 있음
   - 데이터 베이스가 할 수 있는 영역들은 데이터 베이스 쿼리를 활용해 처리하는 것이 더 빠름
5. 최대 절전 모드 활용 
   - 하이버네이트 세션의 1단계 세션 캐시 활용(기본적으로 모든 엔티티는 캐시됨)
   - 2단계 캐시의 경우 공유 캐시 모드를 설정해야 활용 가능(orm.hibernate.annotations.Cache)