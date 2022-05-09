---
title: "[Docker] Docker를 사용하여 postgreSQL 서버를 띄워보자(1)"
date: 2022-05-09 16:04:00 +0900
categories: [Development, DevOps]
tags: [postgreSQL, docker]
---

## 준비물

1. `Docker` 설치: <https://www.docker.com/products/docker-desktop/>

## Docker 기초 문법

- 현재 돌아가고 있는 도커 컨테이너 불러오기
```shell
docker ps
```

- 멈춰있는 상태의 컨테이너까지 불러오기
```shell
docker ps -a
```

- 도커 이미지 불러오기
```shell
docker images
```

- `Docker Hub`에서 이미지 가져오기  
```shell
# 기본 형태
docker pull <image>:<tag>
# postgres:14-alpine 이미지 가져오기
docker pull postgres:14-alpine
```
도커를 사용하면 내 로컬환경에 직접 프로그램을 설치하지 않아도 원하는 이미지를 불러와 해당 이미지로 컨테이너를 실행시켜 내가 원하는 환경을 쉽게 구축할 수 있다.  
우리는 `postgres DB 서버`를 띄우길 원하니까 DockerHub에 들어가서 최신 버전의 `postgres` 이미지를 가져오자.  
여기서 alpine은 초경량화된 배포판을 뜻한다.

## 컨테이너 실행하기

`docker images`명령어로 이미지를 잘 불러왔는지 체크해보고 잘 불러왔다면 `docker run`명령어로 특정 이미지의 컨테이너를 실행시킬 수 있다.

```shell
docker run --name <container_name> -e <environment_variable> -d <image>:<tag>
```

## image vs container ?

`image`를 컨테이너를 찍어내는 틀이라면  `container`를 실제로 돌아가고 있는 환경? 인스턴스?  
이미지와 컨테이너의 관계를 객체지향 프로그래밍에서의 클래스와 인스턴스의 관계 비슷하게 생각하면 될 것 같다.

하나의 이미지를 가지고 수백개의 컨테이너를 찍어낼 수 있다 ㅇㅇ.

## Port mapping

`포트 매핑`을 통하여 특정 포트로 들어오는 트래픽을 도커의 특정 포트로 전달시킬 수 있다.  
`postgres`는 기본적으로 `5432`포트를 사용한다. 로컬의 5432포트로 들어오는 트래픽을 컨테이너의 5432포트로 연결시켜주려면 아래와 같이 하면 된다.

```shell
docker run --name <c_name> -e <env_var> -p <host_ports:c_ports> -d <image>:<tag>
```


```shell
docker run --name postgres14 -p 5432:5432 -e POSTGRES_USER=root -e POSTGRES_PASSWORD=secret -d postgres:14-alpine
```

`docker run` 명령이 성공적으로 실행되면 컨테이너 ID값을 반환해준다.

이후 `docker ps` 명령어로 가동중인 컨테이너 리스트를 확인해보면 컨테이너가 정상적으로 생성된 것을 확인할 수 있다.

## 컨테이너에 명령어 실행시키기

`docker exec` 명령어를 사용하여 컨테이너 안에서 특정 명령어를 실행시킬 수 있다.  
내가 직접 디비서버에 접속해 쿼리를 작성하고 싶은 경우에 활용할 수 있다.  
(`postgres`의 경우 로컬에서 접속하는 경우 비밀번호를 요구하지 않는다고한다.)

```shell
docker exec -it <c_name or c_id> <command> [args]
```

```shell
docker exec -it postgres14 psql -U root
```

위 명령어는 " `postgres14` 컨테이너에서 명령어 `$ psql -U root`를 실행하겠다."라는 의미다.  
그래서 위 명령어를 입력하면 컨테이너의 디비서버에 접속을 할 수 있다.

## 컨테이너 로그 출력

`docker logs` 명령어를 통해 컨테이너의 로그를 불러올 수 있다.

```shell
docker logs <container name or id>
```

