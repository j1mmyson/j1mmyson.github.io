---
title: "[Go] Golang에서 커스텀 에러를 생성해보자"
date: 2021-03-24 09:52:00 +0900
categories: [Development, Go]
tags: [go, golang, error, custom error]
---

# [Go] Golang에서 커스텀 에러를 생성해보자


`go`는 `error`타입을 기본적으로 제공해줍니다. `go`공식페이지에서 `error`타입을 찾아보면 아래와 같습니다. 

```go
type error interface{
    Error() string
}
```

문자열을 반환해주는 `Error()` 메소드를 갖는 객체들은 모두 에러 타입이 될 수 있습니다. 따라서 우리는 아래와 같이 커스텀 에러를 만들어 줄 수 있습니다.

```go
type CustomError struct{
    Code string
    Message string
}

func (e *CustomError) Error() string{
    return e.Code + ", " + e.Message
}
```

`CustomError`구조체에 `code`, `message` 두개의 파라미터를 갖게 하고 에러코드와 에러메세지를 함께 반환해주는 `Error()`메소드를 구현해 주었습니다.

```go
func isSame(a int, b int) (bool, error){
    if a == b{
        return true, &CustomError{Code: "C001", Message: "Not same"}
    }
    return false, nil
}

func main(){
    a := 10
    b := 10
    result, err := isSame(a, b)
    if err != nil{
        fmt.Println(err.Error())
    }
}
```

간단하게 예제코드를 작성해보았는데 사실 함수에 들어온 두 정수형 변수가 같지 않을 때 에러값을 리턴해준다는 것이 적절하진 않지만... 아무튼,, 위와 같은 방식으로 함수안에서 예외상황이 발생했을 때 에러를 반환해주어 내가 잡고자 하는 에러를 핸들링해줄 수 있습니다.