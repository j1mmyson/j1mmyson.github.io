---
title: "[Go/gin/embed] gin 프레임워크에서 embed패키지 사용해서 static file serving하기"
date: 2021-07-23 09:42:00 +0900
categories: [Development, Go]
tags: [golang, embed, gin]
---



`gin` 웹 프레임워크에서는 `embed`패키지를 사용해 만든 `embedFS`타입의 변수를 사용하여 정적파일을 서버에 띄우는 기능이 없(는것 같)다. 그래서 구글링과 `gin`코드를 뒤져서 임시방편으로나마 `embedFS`타입의 변수를 활용하여 템플릿과 정적파일(css, js ...)들을 띄워보았다. 

```
ㄴ web
   ㄴ static/
      ㄴ css/
         - style.css
      ㄴ js/
         - index.js
   ㄴ templates
      - index.html
- main.go
```

폴더 구조는 위 처럼 되어있다고 가정하고 코드를 짜보았다.

```go
//go:embed web/templates/*.html
var templatesFS embed.FS
//go:embed web/static/*
var staticFS embed.FS
```

위 처럼 embed패키지를 사용해 `embed.FS`타입의 변수를 생성해준뒤

```go
func main() {
    r := gin.Default()
    
    // LoadTemplate

    r.GET("/", index)
    // Serving static files
    r.Run(":8080")
}

func index(c *gin.Context){
    c.HTML(http.StatusOK, "index.html", nil)
}
```

로컬호스트:8080으로 접속하면 css, js 파일을 필요로 하는 index.html파일을 클라이언트에게 보여준다. 이제 주석으로 처리한 두 부분 1. `embed.FS로 load template 해주는 함수` 2.`embed.FS타입의 변수로 정적파일 띄우기`를 채워보자.  
1번의 경우는 내가 직접 gin패키지 코드를 보면서 짰고 2번은 구글링을 통해 찾게되었다.

```go
// 1. LoadHTML
func LoadHTMLFromEmbedFS(engine *gin.Engine, em embed.FS, pattern string) {
    templ := template.Must(template.ParseFS(em, pattern))
    
    engine.SetHTMLTemplate(templ)
}

// 2. Serve static files
func main(){
    ...
    r.GET("/static/*filepath", func(c *gin.Context){
        c.FileFromFS(path.Join("/web/", c.Request.URL.Path), http.FS(staticFS))
    })
    ...
}
```

이 방법으로 앞서 고민중이던 문제를 해결하게 되었다. `LoadHTMLFromEmbedFS()`함수의 경우 메서드로 짜면 `r.LoadHTMLFromEmbedFS()`처럼 다른 템플릿 로드 함수들과 통일성있게 사용할 수 있었겠지만 go언어에서 메서드는 해당 구조체가 존재하는 패키지에서만 선언이 가능하여 일단 함수로만 짜두었다.  

한가지 주의해야할 점은 위 방식대로 정적파일들을 서빙하면 html파일에서 불러올 때 경로를

```html
...
	<link rel="stylesheet" href="static/css/style.css">
...
	<script src="static/js/index.js"></script>
...
```

이런식으로 설정해주어야 한다. 

이렇게하여 gin프레임워크에서 `embed.FS`타입의 변수를 활용할 수 있다.

### 전체코드

github: <https://github.com/j1mmyson/gin-embed-example>

```go
package main

import (
	"embed"
	"html/template"
	"net/http"
	"path"

	"github.com/gin-gonic/gin"
)

var (
	//go:embed web/templates/*.html
	templatesFS embed.FS

	//go:embed web
	staticFS embed.FS
)

func main() {
	r := gin.Default()

	LoadHTMLFromEmbedFS(r, templatesFS, "web/templates/*")

	r.GET("/", index)
	r.GET("/static/*filepath", func(c *gin.Context) {
		c.FileFromFS(path.Join("/web/", c.Request.URL.Path), http.FS(staticFS))
	})
	r.Run()
}

func index(c *gin.Context) {
	c.HTML(http.StatusOK, "index.html", nil)
}

func LoadHTMLFromEmbedFS(engine *gin.Engine, em embed.FS, pattern string) {
	templ := template.Must(template.ParseFS(em, pattern))

	engine.SetHTMLTemplate(templ)
}
```

