---
title: "[Docker] Docker 기본 명령어들과 짧은 팁"
date: 2022-09-12 19:00:00 +0900
categories: [Development, DevOps]
tags: [docker]
---

## Container

- 현재 실행중인 컨테이너 리스트 목록 출력하기
```bash
docker ps
```

- 중지된 컨테이너 리스트까지 출력하기
```bash
docker ps -a
```

- 중지된 컨테이너를 시작하기
```bash
docker start <container>
```

- 실행중인 컨테이너를 중지하기
```bash
docker stop <container>
```

- Detach 모드로 컨테이너 실행하기
```bash
docker run -d <image>
```

- 컨테이너의 로그 기록 출력하기
```bash
docker logs <container>
```

- 로그 기록 출력 + 연결하기
```bash
docker logs -f <container>
```

- Attach 모드로 중지되었던 컨테이너 실행시키기
```bash
docker start -a <container>
```

- Detach 모드의 컨테이너에 연결하기
```bash
docker attach <container>
```

- 중지 상태의 컨테이너 삭제하기
```bash
docker rm <container>
```

- 중지 상태의 모든 컨테이너 삭제하기
```bash
docker container prune 
```

- 컨테이너가 중지될 때 자동으로 컨테이너 제거하도록 예약 걸어놓기
```bash
docker run --rm <image>
```

- 컨테이너와 파일 교환하기
```bash
# 컨테이너 -> 로컬
docker cp <container>:/container_folder local_folder
# 로컬 -> 컨테이너
docker cp test/text.txt <container>:/c_folder
```

- 컨테이너 실행 시 이름 부여하기
```bash
docker run --name j1mmyContainer <image>
```



## Image

- 이미지 삭제  
이미지를 삭제할 때에는 해당 이미지를 사용중인 컨테이너(실행 혹은 중지상태의)가 없어야 한다.
```bash
docker rmi <image
```

- 사용중이지 않은 이미지들 제거
```bash
docker image prune
```

- 이미지 생성 시 태그 생성하기
```bash
docker build -t name:latest .
```

- 이미지를 다른 이름/태그로 복제하기
```bash
docker tag <old_image> <new_image>
```

- DockerHub에 내 이미지 푸시하기
```bash
# 1. 도커허브에서 새 레포 생성
# 2. 생성한 레포와 같은 이름의 이미지 생성
# 3. 푸시 명령어로 이미지를 도커허브에 푸시
docker push user_name/image_name:tag
```

- DockerHub에서 이미지 가져오기
```bash
docker pull user_name/image_name:tag
```





## 이런 저런 팁

### 1. *Container id* 값 복사 붙여넣기는 이제 그만

`<container_id>` 를 포함하는 명령어를 실행할 때 id값 전체를 입력할 필요없이 앞에 unique한 몇글자만 입력해도 실행이 된다.  
예를 들어 내가 실행시키려는 컨테이너의 id값이 `abc123` 이고 `abc` 로 시작하는 id값을 가진 컨테이너가 `abc123` 으로 유일한 경우 `docker start abc` 까지만 입력해도 도커는 `abc` 를 `abc123` 으로 인식하여 작업을 수행한다.

### 2. 레이어를 이해하고 더 **효율적**으로 이미지 빌드하기

아래 두개의 도커파일이 있다.  

`Dockerfile_1`

```dockerfile
FROM node:14
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["node", "app.js"]
```

`Dockerfile_2`

```dockerfile
FROM node:14
WORKDIR /app

COPY package.json .
RUN npm install

COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

두 도커파일 모두 같은 이미지를 생성해 주지만 **이미지를 만들 때 걸리는 시간**에는 차이가 있다. 이유가 뭘까?  

도커파일의 경우 한 줄 한 줄을 실행할 때마다 **레이어를 쌓**게되고 이전에 실행했던 작업과 결과가 같으면 **캐싱을 사용**하게된다.  

`Dockerfile_1` 을 보면 로컬의 모든 파일을 가져온 뒤 `RUN npm install` 명령을 시행하게 된다.  
 `RUN npm install` 명령은 로컬의 `package.json` 파일을 보고 필요한 패키지들을 내려받는 명령인데 `package.json` 파일이 변하지 않는다면 항상 같은 작업을 수행하기 때문에 캐싱을 사용하여 작업이 진행된다.  

하지만 1번 도커파일의 경우 `package.json` 파일이 수정되지 않더라도 캐싱을 사용할 수 없는 경우가  발생한다.  
예를 들어 `app.js` 파일이 수정되었다고 했을 때,  `COPY . .` 명령어에서 `app.js` 파일의 변화를 감지하여 캐싱을 사용하지 못하게 되고 이로 인해 그 이후의 모든 작업에서 캐싱을 사용할 수 없게 되기 때문이다.  

반면 `Dockerfile_2` 을 보면 `COPY . .` 명령 이전에 `COPY package.json` 명령을 실행해 준 후 `RUN npm install` 명령을 실행하기 때문에 다른 파일들이 바뀌었더라도 `package.json` 파일이 변하지 않았다면 새롭게 `npm install` 명령을 수행하지 않는다.  

이렇게 도커의 이미지가 레이어 형태로 쌓인다는 것을 이해하고 오래 걸리는 작업들의 명령 순서를 잘 조정하면 더 적은 시간으로 이미지를 빌드 할 수 있다.