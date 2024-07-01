# Transation

## [트랜잭션 전파](https://velog.io/@myspy/Transaction-%EC%A0%84%ED%8C%8C%EA%B0%80-%EB%AD%A1%EB%8B%88%EA%B9%8C)
- 어떤 트랜잭션이 동작 중인 과정에서 다른 트랜잭션을 실행할 경우 <u>어떻게 처리할 것인가</u>에 대한 개념
- ```@Transactional(propagation = Propagation.REQUIRES_NEW)```

### 종류
#### REQUIRED
- Default
- 기본적으로 부모 트랜잭션이 있으면 부모 트랜잭션에 종속
- 부모 트랜잭션이 없으면 새로운 트랜잭션 생성

#### REQUIRES_NEW
- 부모 트랜잭션 무시하고 새로운 트랜잭션 생성
- 부모 트랜잭션은 현재 트랜잭션 종료될 때 까지 대기
- 자기 트랜잭션에서 예외가 발생해 rollback 진행돼도 부모 트랜잭션에 rollback이 전파되지 않음
- 부모 트랜잭션에서 예외가 발생해 rollback 진행돼도 자기 트랜잭션에 rollback 전파되지 않음

#### SUPPORTS
- 부모 트랜잭션이 있을 때만 해당 부모 트랜잭션에 종속
- 부모 트랜잭션이 없으면 트랜잭션이 적용되지 않는다.

#### NOT_SUPPORTED
- 부모 트랜잭션이 있으면 부모 트랜잭션을 대기시키고 트랜잭션 없이 실행
- 부모 트랜잭션이 없으면 트랜잭션 없이 실행된다.

#### MANDATORY
- 부모 트랜잭션이 있을 때만 해당 부모 트랜잭션에 종속
- 부모 트랜잭션이 없으면 예외가 발생한다.

#### NEVER
- 트랜잭션이 적용되면 안되는 경우에 사용
- 부모 트랜잭션이 있으면 예외가 발생한다.
- 부모 트랜잭션이 없으면 트랜잭션 없이 실행된다.

#### NESTED
- 부모 트랜잭션이 있으면 새로운 트랜잭션 생성
- REQUIRED_NEW와는 다르다.
- 부모 트랜잭션의 커밋과 롤백에는 영향을 받지만 자신의 커밋과 롤백은 부모 트랜잭션에게 영향을 주지 않는다.
- 자식 트랜잭션이 실패하면 부모 트랜잭션은 Rollback되지 않는다.
- 부모 트랜잭션이 실패하면 자식 트랜잭션은 Rollback 된다.

### self-invocation
- 트랜잭션이 적용되는 메서드가 동일한 클래스의 다른 메서드를 호출하는 것
- 이 경우 ```@Transactional``` 어노테이션이 적용되어 있더라도 실제로 트랜잭션이 적용되지 않음
- 기본 모드인 proxy가 프록시를 통한 접근만 잡아서 처리할 수 있기 때문

## ```@TransactionalEventListener```
- 동작하는 메서드를 트랜잭션으로 묶어서 처리하는 경우 Transaction의 상태에 따라 발생하는 이벤트를 처리해 주는 이벤트 리스너
- DB를 사용하지 않는 동작도 트랜잭션을 기준으로 생명 주기를 결정할 수 있음
- 트랜잭션이 적용되지 않는다면 이벤트 리스너가 작동하지 않음
- Service에 대한 결합을 낮출 수 있음

### 종류
#### AFTER_COMMIT (default) 
- 트랜잭션이 성공적으로 commit 되었을 때 이벤트 실행
#### AFTER_ROLLBACK 
- 트랜잭션이 rollback 되었을 때 이벤트 실행
#### AFTER_COMPLETION 
- 트랜잭션이 마무리 되었을 때(commit or rollback) 이벤트 실행
#### BEFORE_COMMIT 
- 트랜잭션의 커밋 전에 이벤트 실행

### 동작 순서
- 성공 시 : 'defaultEventListener' -> 'BEFORE_COMMIT' -> '트랜잭션 commit' -> 'AFTER_COMMIT' -> 'AFTER_COMPLETION'
- 실패 시 : 'defaultEventListener' -> '트랜잭션 rollback' -> 'AFTER_ROLLBACK' -> 'AFTER_COMPLETION'

### ```@Transactional(propagation = Propagation.REQUIRES_NEW)```
- @TransactionalEventListener의 경우 event publisher의 트랜잭션 안에서 동작하며, 커밋이 된 이후 추가 커밋을 허용하지 않음
-  insert, update, delete 같은 작업이 필요한 경우 아래 코드와 같이 이벤트 리스너에서 @Transactional(propagation = Propagation.REQUIRES_NEW)를 추가 설정 필요

### 구성 요소
- **event class**
  - 이벤트를 처리하는데 필요한 데이터를 가지고 있는 클래스
- **event publisher**
  - ```ApplicationEventPublisher```
  - ```publisher.publishEvent(new xxxEvent(params..));```
- **event listener**
  - ```@EventListener```

### 비동기 처리(```@Async```)
- ```@EnableAsync``` : 어노테이션을 통해 비동기를 사용하겠다고 선언
- 비동기로 동작하고자 하는 메서드에 ```@Async``` 어노테이션을 설정
