# Docker
## 설치
- [docker 설치](https://www.docker.com/get-started/)

## pull
- **docker hub** - pull -> **image** - run -> **container**
- [dockerhub](https://hub.docker.com/)

## 명령어
- help
  ```shell
  $ docker 명령어 --help
  ```

- 이미지
  ```shell
  $ docker image pull IMAGE-NAME # 태그 정보 생략 시 latest

  $ docker images # 도커 이미지 확인 (정상 설치 확인)

  $ docker rmi [imagename:tag] # 이미지 삭제
  ```
- 컨테이너 생성
  ```shell
  $ docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
  ```
  ```shell
  $ docker run --name mung-mysql -e MYSQL_ROOT_PASSWORD=root -d -p 3306:3306 -d mysql

  $ docker run --name mung-redis -p 6379:6379 -d redis
  ```
- 컨테이너
  ```shell
  $ docker ps # 실행 중인 컨테이너 확인
  
  $ docker ps -a # 모든 컨테이너 확인

  $ docker stop CONTAINER-NAME

  $ docker start CONTAINER-NAME

  $ docker rm CONTAINER-ID # 컨테이너 삭제

  $ docker logs [OPTIONS] CONTAINER-NAME # 로그 출력 [OPTIONS -f : 실시간 출력]

  ```
- 명령어 실행
  ```shell
  $ docker exec [OPTIONS] CONTAINER COMMAND [ARG...] # 명령어 하나 실행

  $ docker -it exec CONTAINER-NAME /bin/sh # 해당 컨테이너에 지속적인 명령어 실행
  
  $ docker -it exec CONTAINER-NAME /bin/bash # 해당 컨테이너에 지속적인 명령어 실행(bash)

  $ exit # 나가기
  ```
  ```shell
  $ docker exec mysql pwd
  ```

## File System

## Docker-compose
```shell
$ docker-compose up

$ docker-compose down

$ docker network inspect

$ docker network inspect NAME
```