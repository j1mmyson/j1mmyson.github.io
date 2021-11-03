---
title: "[Go/embed] embed 패키지로 배포를 더 쉽게"
date: 2021-05-17 10:19:00 +0900
categories: [Development, Go]
tags: [go, golang, embed]
---

# [Go/embed] embed 패키지로 배포를 더 쉽게

## embed package

기존의 고언어에서 어떤 코드 외에 다른 파일이 필요한 실행파일을 실행시키려면 아래와 같이 실행파일 외에 필요한 리소스들이 있어야 실행이 되었다. 

```bash
binaryFile
ㄴ templates
	ㄴ index.html
ㄴ static
	ㄴ index.js
```

이제 1.16.x 버전 이상에서 `embed`패키지를 사용하여 바이너리파일에 리소스 파일들을 포함하여 빌드할 수 있어 실행파일만으로도 프로젝트를 실행시킬 수 있게되어 작업을 진행하고 바이너리 파일을 배포하기 훨씬 쉬워졌다.

## How to use

`embed`패키지는 `string`, `[]byte`, `FS` 3가지 타입만 지원한다.  각 타입마다 간단한 예시코드를 보자.

- string:

  ```go
  import (
      _ "embed"
      "fmt"
  )
  
  //go:embed hello.txt
  var s string
  
  func main() {
      fmt.Println(s)
  }
  ```

- slice of byte:

  ```go
  import (
  	_ "embed"
      "fmt"
  )
  
  //go:embed hello.txt
  var b []byte
  
  func main() {
      fmt.Println(string(b))
  }
  ```

- file system:

  ```go
  import (
  	"embed"
  	"fmt"
  )
  
  //go:embed hello.txt
  var f embed.FS
  
  func main() {
  	data, _ := f.ReadFile("hello.txt")
      fmt.Println(string(data))
  }
  ```

> 주의!  
> `//go:embed`에서 `//`와 `go`사이에 공백이 없어야한다.  
> vscode에서 `ctrl`+`/`를 해버리면 자동으로 주석뒤에 공백한칸이 생겨 실수하기 쉽다.

## web static files

웹 서버에 필요한 파일들을 embed해주는 코드를 짜보았다.

```go
package main

import (
	"embed"
	"html/template"
	"net/http"
)

var tpl *template.Template

//go:embed templates/*
var content embed.FS

func init() {
	tpl = template.Must(template.ParseFS(content, "templates/*"))
}

func main() {
	http.HandleFunc("/", index)
	http.HandleFunc("/second", second)
	http.ListenAndServe(":8080", nil)
}

func index(w http.ResponseWriter, r *http.Request) {
	tpl.ExecuteTemplate(w, "index.gohtml", nil)
}

func second(w http.ResponseWriter, r *http.Request) {
	tpl.ExecuteTemplate(w, "second.gohtml", nil)
}

```

이 외에 `js`, `css`파일들을 띄우기 위해서는 아래처럼 활용해주면 되겠다.

```go
//go:embed static/*
var staticFiles embed.FS

http.Handle("/static/", http.FileServer(http.FS(staticFiles)))
```



