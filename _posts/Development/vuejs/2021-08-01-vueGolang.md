---
title: "[vuejs/golang] When the syntax of golang template and vue.js overlaps"
date: 2021-08-01 01:45:00 +0900
categories: [Development, Vue]
tags: [golang, vue, delimiters]
---

## problems

`main.go`

```go
package main
...

func main() {
    engine := html.New("./templates", ".html")

	app := fiber.New(fiber.Config{
		Views: engine,
	})

	app.Static("/static", "./static")
	app.Use(logger.New())
	app.Get("/", index)

	app.Listen(":8080")
}

func index(c *fiber.Ctx) error {
	return c.Render("index", fiber.Map{
		"message": "hello vue js!",
	})

}
```

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://unpkg.com/vue@next"></script>
    <title>Vue tutorial</title>
</head>
<body>
    
    <div id="app">
        name:  {{ name }}<br> 
        age: {{ age }}
    </div>

    {{ "{{ .message " }}}}

    <script src="static/js/main.js"></script>
    <script>
        const mountedApp = app.mount('#app')
    </script>
    
</body>
</html>
```

`error`

```bash
 ┌───────────────────────────────────────────────────┐ 
 │                   Fiber v2.16.0                   │
 │               http://127.0.0.1:8080               │
 │       (bound on host 0.0.0.0 and port 8080)       │
 │                                                   │
 │ Handlers ............. 4  Processes ........... 1 │
 │ Prefork ....... Disabled  PID .............. 5430 │
 └───────────────────────────────────────────────────┘ 

17:03:20 | 500 |      0s |       127.0.0.1 | GET     | /                | 
template: "index" is an incomplete or empty template
```

## Solution
: `delimiters`를 `['${', '}']`로 바꾸어 주었다.  

`main.js`


```javascript
const app = Vue.createApp({
    delimiters: ['${', '}'],
    data() {
        return {
            name: "son",
            age: 24,
        }
    }
})

console.log("hello")
```

`index.html`

```html
...

<body>
    
    <div id="app">
        name:  ${ name }<br> 
        age: ${ age }
    </div>

    {{ "{{ .message " }}}}
    
    <script src="static/js/main.js"></script>
 ...
    
</body>
</html>
```

