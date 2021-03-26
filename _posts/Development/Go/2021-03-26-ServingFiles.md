---
title: "[Go] 정적파일을 서버에 올려보자"
date: 2021-03-26 11:53:00 +0900
categories: [Development, Go]
tags: [go, file server, StripPrefix, static]
---

# [Go] 정적 파일을 서버에 올려보자

Go언어로 웹 서버를 구현을 해보다가 분명 올바른 위치에 js, css파일이 있는데 서버를 실행시켰을 때 js, css파일을 찾지 못하는 상황이 발생했었다. 경로를 몇번 고쳐보기도 하고 구글링을 해보다가 웹서버에 파일을 올려주어야 한다는 것을 알게 되었다.. 그래서 오늘은 어떻게 서버에 정적 파일들을 서버에 올리는지에 대하여 정리해보려 합니다.

## http.Handle

``` go
func Handle(pattern string, handler Handler)
```

`http.Handle()`함수는 `pattern`와 `handler`를 받아서 `pattern`경로에 받아온 핸들러를 라우팅 해주는 함수입니다. 여기서 `Handler`타입은 `ServeHTTP(ResponseWriter, *Request)`메소드를 갖는 인터페이스 타입으로 데이터를 받아 응답해주는 역할을 해줍니다.

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

## http.FileServer

```go
func FileServer(root FileSystem) Handler
```

`FileServer`는 `FileSystem`타입의 `root`를 받아 `root`디렉토리에 존재하는 컨텐츠들을 제공해주는 `Handler`를 반환해준다. 아래 코드를 실행시켜보면 경로 "/"에서 `http.Dir(".")`(현재 위치)의 파일들을 제공해주는 것을 볼 수 있습니다.

```go
func main(){
    http.Handle("/", http.FileServer(http.Dir(".")))
    
    fmt.Println("Serving ... ")
    http.ListenAndServe(":8080", nil)
}
```

특이한 경우로 해당 경로에 `index.html`파일이 존재하면 `index.html`로 redirects 해준다고 합니다.  

```bash
- main.go
- static
	- index.html
	- style.css
	- index.js
```

위와 같은 경로에서 아래 코드를 실행시키면 `static`폴더의 파일들이 서버에 올라가게 되면서 `index.html`을 감지하여 css, js 가 제대로 적용된 `index.html`페이지로 redirect 되는 것을 확인할 수 있겠습니다.

``` go
func main(){
    http.Handle("/", http.FileServer(http.Dir("/static")))
    
    fmt.Println("Serving ...")
    http.ListenAndServe(":8080", nil)
}
```

## http.StripPrefix

끝으로 `http.StripPrefix`를 사용하여 내가 올리려는 디렉토리를 대체 이름의 디렉토리로 서버에 올릴 수 있습니다. 아래처럼 해주어 현재 작업하고 있는 경로에 존재하는 폴더 `/assets`를 서버의 `/exam` 폴더로 올라가게 해줄 수 있습니다. 

```go
func main(){
    http.HandleFunc("/", index)
    http.Handle("/exam/", http.StripPrefix("/exam", http.FileServer(http.Dir("./assets"))))
    http.ListenAndServe(":8080", nil)
}

func index(w http.ResponseWriter, req *http.Request) {
    w.Header().Ser("Content-Type", "text/html; charset=utf-8")
    io.WriteString(w, `<img src="/exam/gopher.jpg">`)
}
```

실행해보면 index페이지에서는 `/exam`폴더 아래의 `gopher.jpg` 를 찾고 있는데 제대로 받아오는 것을 확인할 수 있습니다. 