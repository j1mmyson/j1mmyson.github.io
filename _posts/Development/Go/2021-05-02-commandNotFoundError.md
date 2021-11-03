---
title: "[Go] Command 'go' not found error"
date: 2021-05-02 14:56:00 +0900
categories: [Development, Go]
tags: [go, golang, command go not found]
---

# [Go] Command 'go' not found 에러를 해결해보자
## #2021-05-02
Golang 설치를 완료하였더라도 컴퓨터를 재시작 하였을 때 PATH설정이 제대로 적용되어있지 않을 수 있다.  
그래서 매번 `export PATH=$PATH:/usr/local/go/bin`을 실행시켜준 뒤 go 명령어를 사용했었는데 `$HOME/.profile`파일에 `export PATH=$PATH:/usr/local/go/bin` 을 추가해주면 매번 재시작 할때마다 PATH설정이 되어서 번거롭게 `export`명령을 실행시켜 줄  필요가 없어진다.

``` bash
vi ~/.profile
```

`.profile`을 열어주면 대충 아래와 같이 생겼는데 여기서 가장 아래에 `export`명령어를 추가해주면 된다.

```bash
# ~/.profile: executed by the command interpreter for login shells.
# This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login
# exists.

...

# Nodejs
VERSION=v14.15.0
DISTRO=linux-x64
export PATH=/usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin:$PATH

#Golang
export PATH=$PATH:/usr/local/go/bin
```

`go version` 명령어를 실행하여 go 명렁어가 제대로 실행되는지 확인해본다.