---
title: "Handling Error in Golang"
date: 2021-09-09 12:26:00 +0900
categories: [Development, Go]
tags: [golang, handling error, error]
---

이 글은 **Tucker**님의 고언어 강의 **Go 언어가 온당**을 듣고 정리한 내용을 토대로 작성하였습니다.  

**[Go언어가 온당 Youtube](https://www.youtube.com/playlist?list=PLy-g2fnSzUTBHwuXkWQ834QHDZwLx6v6j)**
[![Thumnail](https://github.com/j1mmyson/j1mmyson.github.io/blob/master/assets/img/posts/go/ondang/tuckerGolang.jpeg?raw=true)](https://www.youtube.com/playlist?list=PLy-g2fnSzUTBHwuXkWQ834QHDZwLx6v6j)

--- 
**Go언어**에서 **에러를 다루는 방법**은 크게 두가지가 있다.

1. 프로그램을 죽이는 방법
2. 에러를 처리해서 프로그램을 지속시키는 방법

**개발 단계**에서는 **빨리 오류를 찾는게 좋으니**까 **오류가 나면 빨리빨리 죽이는** 경우가 많음.  
**서비스 배포 단계**에서는 **UX를 향상**시키기 위해 **프로그램을 지속**시키는 방법이 좋을 수 있음.

> ex) 
>
> 1. 항공관제시스템을 만든다고 했을 때 오류가 났다고 프로그램을 죽여버리면 위험한 사황이 발생할 수 있으므로 프로그램이 죽지 않게 만들어줘야함.
> 2. 마찬가지로 인공심장 머신에 들어가는 프로그램처럼 프로그램이 죽으면 치명적인 경우 죽지않는 프로그램을 설계해야함.



golang에서는 에러가 발생했을 때 내부에서 처리하는 것이 아닌 **error값을 반환해주어 사용자가 에러를 처리할 수 있게끔** 설계되어있다.

## Custom Error
---

`fmt.Errorf(formatter string, ...interface{}) error` 함수 or `errors.New(text string) error`함수 사용

나는 후자가 편했다.

## error type
---

```go
type error interface {
    Error() string
}
```

`Error() string` 메소드를 가지면 어떤 타입이든 `error` 가 될 수 있다.

## Error Wrapping
---

```go
func wrappingExam(filename string) error {
    f, err := os.Open(finame)
    if err != nil {
        return fmt.Errorf("error: %w", err)
    }
    defer f.Close()
    return nil
}
```

래핑이라는 말 그대로 한 겹씩 에러를 감싸준다고 생각하면 된다. 후입선출이라고 볼 수 있겠다. (마지막으로 감싼 에러가 가장 먼저 벗겨짐.)  
호출자 입장에서는 **어디서 에러가 발생**했는지 알 수 없음.  
맨 아래에서부터 발생한 에러를 감싸서(wrapping) 위로 올림. (반복)  
이제 여러겹으로 감싸진 에러를 풀기위해 `errors.As()`로 원하는 에러를 꺼내올 수 있다.

## Panic
---

처리하기 힘든 에러를 만났을 때 **프로그램을 조기 종료**하는 방법. (빠르게 죽는 방법)  
**빠르게 종료시켜서 오류를 해결하기 위해**서 사용 !

**panic**을 사용하면 **프로그램이 바로 종료되면서 어디서 문제가 생겼는지 로그로 바로 볼 수 있어**서 에러를 좀 더 명확하고 찾기 쉬워진다.

```go
func panic(interface{})
// 모든 타입 사용 가능
panic(42)
panic("hello panic")
...
```

## 패닉 전파 그리고 복구
---

프로그램을 개발할 때는 문제를 빠르게 파악해서 해결하는게 중요  
서비스 배포 시작 뒤에는 최대한 안죽는게 중요함 !  

> 페이스북이 오류가 발생시마다 웹서버가 종료된다면 사용자 경험이 안좋기 때문에 이럴때 복구를 해야할 필요가 있다.

개발 단계에서 `panic`을 사용해서 개발하다가 서비스 배포를 한다고 했을 때 이 모든 panic함수를 지우고 error를 반환하는 방법으로 바꾸기 어려울 수 있기 때문에 이럴 때 복구(recover)를 해준다.

```go
func recover() interface{}
// panic()에 넣어주었던 인자를 반환값으로 반환해줌.
// defer와 함께 사용된다.
```

## Go는 SEH를 지원하지 않는다.
---

SEH(Structured Error Handling) 구조화된 에러.

```
try{

} catch {

}
```

왜 안쓰는가?

1. 성능문제
2. 에러를 먹어버리는 문제 (오히려 에러 처리를 등한시 한다)

## 에러 처리는 매우 중요함!
---

보통 에러 처리를 귀찮아 하고 코드를 지저분하게 만든다고 생각하여 등한시 하거나 무시하지만  
에러처리는 매우 중요하다.  
에러 처리 역시 중요한 코드 일부분으로 여기고 에러를 반환하는 함수에서 반환되는 에러를 제대로 처리해야 한다.   
개발 단계에서의 에러는 드러내야 하고 조기에 발견하여 더 큰 문제를 미연에 방지해야 한다.!!