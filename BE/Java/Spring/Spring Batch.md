## Batch? Spring Batch?
### Batch
- 특징
  - 일정 기간으로 스케줄링 하여 처리
  - 유저와 상호 작용 없이 동작
  - 대용량 데이터 처리
- 고려사항
  - 최대한 단순화
  - 메모리에 무리 가지 않는 수준으로 처리
  - **데이터 무결성 고려**
  - DB 쿼리 최적화
  - 백업 중요
- Use case
  - 오래된 데이터 삭제
  - 통계 데이터 집계
  - 읽기와 쓰기의 분리
      - **CQRS 패턴** : 명령(Command)과 질의(Query)의 책임(Responsebilitiy)을 분리(Segregation)한다
  - 데이터 상태 변경
  - 데이터 마이그레이션
  - 모니터링

### Spring Batch
- Spring Source가 Accenture사와 협력하여 개발한 Batch Framework
- 엔터프라이즈 환경에서 강력한 Batch Application을 개발할 수 있도록 설계
- Logging / Tracing, Transaction Management, Job Processing Statistics, Job Restart, Skip, Resource Management 등 대량의 레코드 처리에 필수적인 재사용 가능한 기능 제공
- 최적화 및 파티셔닝 기술을 통해 대용량 및 고성능 배치 작업을 가능하게 하는 고급 기술 서비스 및 기능 제공
- 단순하고 복잡한 대용량 배치 작업은 확장성이 뛰어난 방식으로 프레임 워크를 활용하여 많은 양의 정보를 처리
- Spring Framework 사용
- Run, Job, Application 구분
- interface로 공통 핵심 실행 서비스 제공
- Maven 사용하여 빌드 된 Application과 완전히 분리된 아키텍처 JAR로 간단한 배포 모델 제공
  
# Spring Batch
## Job
- 작업
  - 하나의 Job Flow에서 여러 Step을 가짐
  - ```java
        // xml로도 가능
        @Bean
        public Job footballJob(JobRepository jobRepository) {
            return new JobBuilder("footballJob", jobRepository)
                            .start(playerLoad())
                            .next(gameLoad())
                            .next(playerSummarization())
                            .build();
        }
    ```
  - Job 설정
    - 작업 이름
    - Step 인스턴스의 정의와 순서
    - 작업 재시작 가능
- **JobInstance**
  - 논리적 작업의 실행 단위
  - 매일 실행되는 배치일 경우 매일 하나의 JobInstance 생성
  - JobParameters에 의해 식별됨
- **JobParameters**
  - 배치 작업을 시작하는데 사용하는 파라미터 세트를 가짐
  - Job Instance = Job + JobParameters
  - ```java -jar build/libs/batch.jar --job.name=jobName +createdDate=2024-06-25```
    - \+ 붙은 파라미터는 식별 가능한 파라미터
    - \- 붙은 파라미터는 일반 파라미터
  - ```@Value("#{jobParameters[createdDate]}") String createdDate```
- **JobParametersIncrementer**
  - JobParameter를 자동으로 Increment하기 위해 사용
  - JobExcution에 문제가 있어 재실행 어려운 경우 JobParameters를 Increment하여 처음부터 다시 실행하기도 함
  - 무조건 증가시키고 동일한 JobParameters의 Job을 여러 번 수행해도 괜찮은 경우 있음
  - 보통 RunIdIncrementer 사용, JobParametersIncrementer 인터페이스를 커스텀하게 구현하여 사용
- **JobExecution** : 실제 실행 단위
  - JobInstance는 논리적인 실행 단위
  - Job은 작업과 실행 방법 등의 설정을 정의한다면, JobInstance는 올바른 시작을 위해 실행의 논리적인 단위, JobExecution은 실제 물리적인 실행의 단위
  - 매일 실행되는 배치일 경우 성공하지 않았을 경우 완료되는 것으로 간주하지 않아 하루에 여러 JobExecution이 생성될 수 있음
  - JobExecutionContext는 개체 범위에 지정된 상태를 저장할 수 있도록 개발자에 제공
- **ExecutionContext**
- **JobRepository**
  - Batch 메타 데이터 엔티티의 지속성을 담당하는 저장소
  - JobLauncer, Job, Step 구현체들을 위해 CRUD 작업 제공
  - StepExecution, JobExecution 구현체들은 Repository에게 전달되어 저장됨
  - DefaultBatchConfigurer를 상속받아 설정 가능
  - ```@EnableBatchProcessing```
- **JobLauncher**
  - JobRepository로 부터 유효한 JobExecution을 획득하고 Job 실행
  - JobLauncher 인터페이스는 호출이 동기 또는 비동기적으로 실행되는지 보장X
  - 작업이 실행되는 방법을 완전히 이해하도록 특정 구현에 대한 javadocs 확인 권장
  - DefaultBatchConfigurer를 상속받아 설정 가능
  - ```java
        public interface JobLauncher {

            public JobExecution run(Job job, JobParameters jobParameters)
                throws JobExecutionAlreadyRunningException, JobRestartException,
                    JobInstanceAlreadyCompleteException, JobParametersInvalidException;
        }
    ```

## Step
- 단계
  - 모든 Job은 하나 이상의 Step을 가짐
  - 설계는 개발자 재량
  - ```java
        @Bean
        public Step step1(JobRepository jobRepository, 
            PlatformTransactionManager transactionManager) {
            return new StepBuilder("step1", jobRepository)
                        .tasklet(myTasklet(), transactionManager)
                        .build();
        }
    ```
- **StepExecution**
  - Step의 실행 단위
  - Step이 실제로 시작될 때 생성
  - StepExecutionContext는 개체 범위에 지정된 상태를 저장할 수 있도록 개발자에 제공

### Spring Batch - Schema
- BATCH_JOB_INSTANCE
  - 작업이 시작될 때 마다(JOB_NAME, JOB_KEY)
- BATCH_JOB_EXECUTION
  - 작업이 다시 시작될 때 마다(JOB_INSTANCE_ID)
- BATCH_STEP_EXECUTION
  - 커밋 간격과 Step 시작과 끝(VERSION)
  - 각 Step 시작 전(STEP_NAME, JOB_EXECUTION_ID)

## Tasklet
- 하나의 Step에서 단일 Task를 수행
- Tasklet 인터페이스 구현
- TaskletStepBuilder
- **ChunkOrientedTasklet**
  - 하나의 Step에서 읽기, 처리, 쓰기의 과정으로 Task 수행
  - Page Size 단위로 읽고 처리한 데이터가 Chuck Size에 도달하면 쓰고 Commit 처리
  - SimpleStepBuilder
- Tasklet 사용하는 경우
  1. 도메인 영역에 대용향 처리 관련 코드를 이미 가지고 있을 때
  2. INSERT, UPDATE, DELETE문 한 번에 끝나는 아주 간단한 비즈니스
  3. (읽기, 처리, 쓰기로 정형화할 수 없을 정도로 너무 복잡하여) 생각하고 싶지 않을 때?
  4. 내가 더 잘 짤 수 있을 자신이 있을 때?

### [Chunk-oriented Processing](https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html#page-title)
- Spring Batch에서 가장 일반적으로 사용하는 Tasklet 구현체
- Chunk 지향 처리 : 한 번에 1건 씩 데이터를 읽어서 Chunk라는 데이터 덩어리를 만들어 트랜잭션 처리
- 아래 interface를 미리 구현한 class를 사용하거나 상속받을 후 직접 구현하여 개발
  - **ItemReader** : Item을 Page 단위로 읽음 (where)
    - [JdbcPagingItemReader](https://docs.spring.io/spring-batch/reference/readers-and-writers/database.html#JdbcPagingItemReader)
      - PagingQueryProvider에서 빌드한 SQL을 실행하여 요청된 데이터 검색
      - 실행 간에 데이터 손실되지 않도록 하려면 정렬 키에 고유한 키를 지정하는 것이 중요
    - [JpaPagingItemReader](https://docs.spring.io/spring-batch/reference/readers-and-writers/database.html#JpaPagingItemReader)
      - 메모리 사용량을 줄이기 위해 각 페이지를 읽은 후 영속성 컨텍스트를 플러시하고 삭제
      - 반드시 EntityManagerFactory를 설정해야 함
      - Page size는 Chunk size와 동일하게 설정하도록 권장
    - [RepositoryItemReader](https://docs.spring.io/spring-batch/reference/readers-and-writers/item-reader-writer-implementations.html#repositoryItemReader)
      - PagingAndSortingRepository, Sort, 0 보다 큰 pageSize 설정
      - Page size는 Chunk size와 동일하게 설정하도록 권장
  - **ItemProcessor(optional)** : 입력 Item에 대해 가공하여 출력 Item을 생성 
    - item 변환 처리
    - 프로세서는 반드시 필요한 것은 아님
    - null 반환하면 item 처리되지 않음
  - **[ItemWriter](https://docs.spring.io/spring-batch/reference/readers-and-writers/item-writer.html#page-title)** : 처리된 Item을 Chunk 단위로 씀
    - JPA EntityManagerFactory를 사용하여 영속성 컨텍스트의 엔티티 병합
    - 트랜잭션 내 write(List) 호출해야 함
    - 속성 설정된 후 Thread safe
    - ```java
        public interface ItemWriter<T> {

            void write(Chunk<? extends T> items) throws Exception;

        }
      ```
    - [JdbcBatchItemWriter](https://docs.spring.io/spring-batch/reference/readers-and-writers/item-reader-writer-implementations.html#jdbcBatchItemWriter)
      - NamedParameterJdbcTemplate의 일괄 처리 기능을 사용하여 제공된 모든 항목에 대한 일괄 처리를 실행하는 ItemWriter

### [Repeat](https://docs.spring.io/spring-batch/reference/repeat.html)
1. **RepeatOperations** : 일괄 작업에 대한 액세스를 제공하는 인터페이스
2. **RepeatTemplate** : RepeatOperations를 구현한 클래스
3. **RepeatCallback** : 일괄 작업을 위한 콜백 인터페이스
4. **RepeatStatus** : 처리가 완료되었는지 여부를 나타냄(RepeatStatus.CONTINUABLE, RepeatStatus.FINISHED)
5. **CompletionPolicy** : 배치 완료 정책을 위한 인터페이스

### [Listener](https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/intercepting-execution.html)
1. **ItemReadListener** : item 읽기에 대한 리스너 인터페이스
2. **ItemProcessListener** : item 처리에 대한 리스너 인터페이스
3. **ItemWriteListener** : item 쓰기에 대한 리스너 인터페이스 
4. **StepExecutionListener** : Step의 생명주기에 대한 리스너 인터페이스
   

## [Parallel Processing](https://docs.spring.io/spring-batch/reference/scalability.html)
1. **[Multi-threaded Step](https://docs.spring.io/spring-batch/reference/scalability.html#multithreadedStep)**
    - taskExecutor 설정
    - Spring Batch는 대부분 다중 스레드 환경을 위해 설계 되지 않음
    - 주의해서 사용
2. **[Parallel Steps](https://docs.spring.io/spring-batch/reference/scalability.html#scalabilityParallelSteps)**
    - 어플리케이션 로직을 step으로 분할하여 병렬 처리
3. **[Remote Chunking](https://docs.spring.io/spring-batch/reference/scalability.html#remoteChunking)**
   - 하나의 Manager와 여러 개의 Worker가 원격으로 분리된 구조로 Manager에 부하가 발생하면 취약한 구조
   - READ < WRITE 인 경우
   - Manager : read
   - Worker : Chunk 단위로 처리하여 Item write
4. **[Partitioning](https://docs.spring.io/spring-batch/reference/scalability.html#partitioning)**
   - Step 실행을 분할하고 원격으로 실행하기 위한 SPI(동기식 직렬 통신 방식) wprhd
   - Manager는 하나의 Step 인스턴스, 여러 개의 Worker는 모두 동일한 인스턴스로 Manager를 대신하여 작업


## Scheduling
- Scheduling
  - 수작업
  - Linux Crontab, Windows Task Scheduler
  - Spring scheduler, Quartz
  - Spring Cloud Data Flow
  - Airflow, jenkins, TeamCity ...
- Logging : 필요한 로그를 잘 남기자!
  - 배치 관리 도구
  - 애플리케이션 로그
  - 배치 DB 실행 로그
  - 배치 결과 알림(메일, 슬랙, ...)
- 유의사항
  - 배치 테스트 환경 구성(귀찮다면 Job이라도 돌릴 코드만이라도..)
  - 로그를 의미 있게 잘 남기자
  - 어디서 배치가 돌고 있는지 꼭 기록하고 공유!
  - 많은 배치가 자주 동작한다면 메타 데이터를 주기적으로 지워주거나 최적화해야 할 수도..
  - 멀티 모듈 프로젝트도 한 번 고려해보자
  - 잘 쓰지 않은 클래스나 모듈을 넣었다면 가이드를 작성해보자
  - 병렬 처리시에는 항상 DeadLock 생각!

## UseCase
- **통합 배치 스케줄러**
  - 하나의 어플리케이션에 Job을 만들고 Scheduled을 설정하여 실행
  - API를 만들어서 수동으로 동작하게 함
  - 소, 중규모 서비스 운영 시에 탁월
  - 규모가 커지기 시작하면 판단 하에 분리
- **테이블 구조 개선을 위해 서비스 무중단 데이터 이전**
  - 개선을 목적으로 기존의 테이블을 버리고 신규 테이블로 이전
  - 데이터 마이그레이션 필수
  - 경우에 따라 API에서 Legacy 테이블과 신규 테이블에 Double Write 필요
  - 이전 테이블에 대해 100% 정확한 영향도 분석 필수
- DB 전체 이관
  - 데이터 마이그레이션 필수
  - 스키마 변경이 적은 테이블은 ETL 툴 활용
  - 스키마 변경이 큰 테이블은 application batch 활용