---
title: "[AWS/Go] EC2에서 Go server를 지속적으로 호스팅해보자"
date: 2021-04-29 23:51:00 +0900
categories: [Development, DevOps]
tags: [aws, ec2, go, golang, service, systemd]
---


# [AWS] AWS EC2에서 Go binary 서비스하기

## #2021-04-29

이전에 go 언어로 짠 웹 서버 코드를 빌드 하여 aws ec2 인스턴스로 보내준 후 바이너리 파일을 실행시켜서 웹 서버를 호스팅을 해보았다.  
하지만 이렇게 서비스를 호스팅 했을 때 바이너리 파일을 실행하고 있는 세션을 나가게 되면 서비스 호스팅이 되지 않아 내가 항상 서버를 호스팅 하고 싶다면 내 컴퓨터를 계속 켜놓아 세션을 켜진 상태로 유지해 주어야 했다.  
이러한 문제점을 `system.d`를 활용해 해결할 수 있다는 것을 알게 되었고 비로소 바이너리 파일을 실행하고 있는 터미널 세션에서 나가도 호스팅을 유지할 수 있게 되었다.  
오늘은 그 방법에 대해 정리해 보았다.

## **system.d**로 호스팅 유지하기

1. 바이너리 파일을 aws ec2 인스턴스로 보낸다. (scp명령어 혹은 git 명령어 사용)

2. 인스턴스의 `/etc/systemd/system`경로에 <service_name>.service 파일을 만들어준다.

   ```bash
   cd /etc/systemd/system
   sudo vi goweb.service
   ```

   `goweb.service`

   ```bash
   [Unit]
   Description=CRUD.gg Server
   
   [Service]
   ExecStart=/home/ubuntu/Go_CRUD/server # 실행할 파일의 경로
   WorkingDirectory=/home/ubuntu/Go_CRUD # 실행할 파일이 존재하는 폴더의 경로
   User=root
   Group=root
   Restart=always
   
   [Install]
   WantedBy=multi-user.target
   ```

3. 아래 명령어를 실행하여 서비스 배포 시작

   ```bash
   sudo systemctl enable goweb.service
   sudo systemctl start goweb.service
   sudo systemctl status goweb.service # goweb.service가 잘 실행되는지 확인
   ```

4. 세션에서 나간 후에도 서비스가 잘 배포되고 있는지 확인해본다.