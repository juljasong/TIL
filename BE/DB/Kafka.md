# [Kafka](https://velog.io/@kidae92/Apache-Kafka-%EC%A3%BC%EC%9A%94-%EC%9A%94%EC%86%8C1Producer-Consumer-Topic)
## Message Oriented Middleware
- 독립된 서비스 간 데이터를 주고받을 수 있는 형태의 미들웨어
- 서비스 간 결합도 낮추기 위함
- **Kafka**(대용량 처리 특화), **RabiitMQ**(라우팅 가능) 등

## Message Queue
- 사용 이유
  - Asynchronous(비동기 처리)
  - Decoupling(탈동조화)
  - Scalability(확장성)
- 모델
  - **Point to Point** :: RabiitMQ
    - [Sender] - [Message Queue] - [Receiver] * n
      - **fire and forget** :: 메시지 유실 가능성 높음
      - **request reply** :: 메시지 유실 가능성 낮음. request queue, response queue 존재
  - **Pub/Sub** :: **kafka**, RabiitMQ
    - [Publisher/Producer] - [Message Queue] - [Subscriber/Consumer]
    - Topic : 이벤트를 분류하는 단위. 한 개 이상의 파티션으로 이루어짐
      - Ack
        - 0 : Producer -> Leader Partition에 전송 후 응답값 받지 않음(처리 속도 빠름, 메시지 유실 가능성 있음)
        - 1 : Producer -> Leader Partition에 전송 후 Leader Partition의 응답 받음(중간)
        - all : Producer -> Leader Partition에 전송 후 Replication 정상 응답 까지 기다림(처리 속도 느림, 메시지 유실 가능성 없음)
    - Disk I/O
    - Page Cache를 통해 성능 개선
    - 메모리를 많이 잡아 먹음 -> 분리 필요
    - 데이터 전송 배치 처리 (데이터 묶어서 처리) -> 리소스 소모 최소화
    - [Kafka Streams](https://velog.io/@holicme7/Apache-Kafka-Kafka-Streams-%EB%9E%80)
      - Kafka 내부 파이프라인
    - Zookeeper : Kafka 메타데이터 관리 툴. Broker 관리
    - Broker : 실제 데이터 저장 장소

```shell
  $ kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic TOPIC_NAME # 토픽 생성
  
  $ kafka-topics.sh --list --bootstrap-server localhost:9092 # 생성된 토픽 확인

  $ kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic payment_cancel_request --from-beginning
```