---
title: "[Js] var, let, const의 차이점에 대해 알아보자"
date: 2021-03-24 15:31:00 +0900
categories: [Development, Javascript]
tags: [js, javascript, var, let, const]
---

# [Js] var, let, const의 차이점에 대해 알아보자

`javascript`에서는 `var`, `let`, `const` 세가지 키워드로 변수를 선언해 줄 수 있습니다.  
그런데 자바스크립트에서 `var`키워드로 변수를 생성하는 방법은 지양해야한다고 합니다. 그렇다면 왜 `var`키워드를 쓰면 안되는지, 이 세가지 키워드의 차이점은 무엇인지에 대해 알아봅시다.

## 1. 재선언

자바스크립트에서 `var`는 계속해서 재선언이 가능합니다. 때문에 긴 코드를 짤 때 실수로 같은 이름을 가진 변수를 선언해주어도 아무런 에러, 워닝을 알려주지 않기 때문에 예상치 못한 결과값 발생을 불러올 수 있습니다.

```javascript
var x = 0;
var x = 10; // error가 발생하지 않음.
```

`var`와는 달리 `let`, `const`는 재선언이 되지 않습니다. `var`보다는 `let`, `const`로 변수를 선언해 주어야 하는 첫번째 이유입니다. `let`과 `const`의 차이는 재할당의 여부입니다. `let`은 재할당이 가능하지만 `const`는 재할당이 불가능하며 `const`는 반드시 선언과 함께 값을 초기화 해주어야 합니다.

```javascript
let y = 0;
let y = 10; // error

const z = 5;
const z = 10; // error

const q; // error
```

## 2. 호이스팅

호이스팅이란 `var`의 선언이나 함수의 선언을 해당 스코프에서 최상단으로 끌어 올려 먼저 선언하는 것을 말합니다. 호이스팅은 변수, 함수의 선언만 끌어 올리고 할당은 무시합니다(*). 호이스팅은 `var`, `function 선언식`만을 대상으로 합니다. 글로는 이해가 어려울 수 있으니 코드를 봅시다.

```javascript
console.log(x); // undefined, 선언만 끌어오고 10이 할당되지 않았음을 볼 수 있다.
var x = 10;
```

위와 같은 경우 `var`선언이 호이스팅되어 스코프의 상단으로 올라가 `consol.log()`가 실행되기전에 미리 선언이 된 것 처럼 작동하여 에러가 발생하지 않습니다. 아래와 같은 좀더 복잡한 상황도 있습니다.  
`if(false)`에는 절대 도달할 수 없지만 `var v`가 호이스팅되어 `console.log()`에서 에러가 발생하지 않는 것을 확인할 수 있습니다. (오...)

```javascript
foo(); 

function foo(){
    console.log("v", v);
    if(false){ // 절대 도달할 수 없는 부분
        var v;
    }
}
```



## 3. 스코프

`var`: 함수 스코프 / `let`, `const`: 블록 스코프를 가집니다.  
스코프에 대해서는 아래 글에 조금더 자세히 정리 해놓았습니다.  
[[Js]자바스크립트 (3)스코프](https://j1mmyson.github.io/j1mmyson.github.io/posts/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-(3)%EC%8A%A4%EC%BD%94%ED%94%84/)
