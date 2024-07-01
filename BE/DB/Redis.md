## 캐시
- 임시로 데이터를 저장하는 공간
- for 성능 향상

# Redis
- Key-value
- 인 메모리 데이터 스토어 -> 휘발성 + Redis 자체 영속성 지원(안정성 낮음)
- 캐시 서버 용도로 사용됨
- 다양한 형태의 데이터 타입 지원
- 같은 요청이 여러번 들어오는 경우 **성능 향상**
- SpinLock을 활용한 **동시성 제어**
- 캐시 메모리 사용이 적합한 지 판단 필요
  1. 동일한 요청이 자주 들어오는가?
  2. 자주 변경되는 데이터 인가?


### [redis 명령어](http://redisgate.kr/redis/introduction/redis_intro.php)
  ```shell  
    $ set [KEY] [VALUE]
    $ get [KEY]
    $ del [KEY]
    $ keys *
  ```