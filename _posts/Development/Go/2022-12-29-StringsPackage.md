---
title: "[Go] strings package 파헤치기 (상)"
date: 2022-12-29 22:35:00 +0900
categories: [Development, Go]
tags: [go, golang, strings]
---

예전부터 **오픈소스를 까보고 분석하는 글**을 꼭 써보고 싶었는데 이번 글을 시작으로 종종 올리게 될 것 같습니다.  
첫 글이니까 가장 많이 쓰이면서 쉬운 편에 속한다 생각되는 **문자열을 다루는 패키지**  `strings` 대해 알아봅시다!

## 목적
---
내가 고언어를 다루다가 문자열을 다뤄야 할 때 보고 금방 되새김질하기 위함.  
따라서 `strings`의 모든 기능에 대해 서술하지 않음. 내가 자주 쓸 것 같은 것만!

## 목차
---
- [**Clone**: 문자열의 복사본을 만들어줘](#clone-문자열의-복사본을-만들어줘)
- [**Compare**: 두 문자열을 비교해줘](#compare-두-문자열을-비교해줘)
- [**Count**: 내가 찾는 글자가 몇개나 있어?](#count-substr이-s안에-몇개-존재해)
- [**Fields**: 공백을 기준으로 문자열 나눠줘](#fields-공백을-기준으로-문자열을-나눠줘)
- [**FieldsFunc**: 나만의 기준으로 문자열을 나눌래](#fieldsfunc-나는-공백말고-다른-기준으로-문자열을-나누고-싶어)
- [**Contains**: 내가 찾는 문자열이 있어?](#contains-substr이-s안에-존재해)
- [**Join**: 문자열을 합쳐줘](#join-문자열을-합쳐줘)
- [**Map**: 모든 글자에 함수를 적용시키겠어](#map-모든-글자에-mapping을-거친-문자열을-반환해줘)
- [**Replace**: 원하는 글자만 바꿔줘](#replace-s에서-n개-만큼old를-new로-바꿔줘)
- [**Split**: `sep`으로 문자열을 나눠줘](#split-sep으로-s를-나눠줘)
- [**Trim**: 양쪽에서 `cutset`에 속하는 문자들을 지우고싶어](#trim-양쪽에서-cutset을-없애고-싶어)

## Clone: 문자열의 복사본을 만들어줘
---
문자열을 깊은 복사[^deepcopy] 해줍니다.  
그렇기 때문에 당연히 새로운 메모리를 할당받게 돼요.  

내부적으로는 원본 `s`와 같은 길이의 문자열을 만들고 내용을 복사해 줍니다.
```go
func Clone(s string) string {
	if len(s) == 0 {
		return ""
	}
	b := make([]byte, len(s))
	copy(b, s)
	return *(*string)(unsafe.Pointer(&b))
}
```
### example code
Cloned된 문자열과 원본 문자열은 당연히 **독립**적!
```go
func main() {
origin := "my name is byungwook"
copied := strings.Clone(origin)

copied = "my name is jimmy"

fmt.Println("origin: ", origin)
fmt.Println("copied: ", copied)
}

================
Output:

my name is byungwook
my name is jimmy
```

## Compare: 두 문자열을 비교해줘
---
간단합니다.  
두 문자열을 비교해 주어 같다면 0, 그렇지 않다면 어떤 문자열이 사전 순으로 빠르냐에 따라 -1, 1을 반환해 줍니다.
```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    a := "apple"
	b := "apple"
	c := "banana"

	fmt.Println(strings.Compare(a, b))
	fmt.Println(strings.Compare(a, c))
}

=======================
Output:

0
-1
```


## Count: substr이 s안에 몇개 존재해?
---
`substr`이 `s`안에 몇 개 존재하는지 반환해 줍니다.  
특이한 점은 `substr == ""`일 경우 `len(s) + 1`을 반환해 줍니다.  
내부적으로 `Index(s, substr)`를 사용하여 **KMP 알고리즘**을 반복해 반복되는 횟수를 구하며 시간 복잡도는 `O(n), n = len(s)`입니다.

### example code
```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    apple := "apple"
    banana := "banana"

    fmt.Println(strings.Count(banana, "na"))
    fmt.Println(strings.Count(apple, "a"))
    fmt.Println(strings.Count(apple, ""))
}

====================
Output:

2
1
6
```
 
## Fields: 공백을 기준으로 문자열을 나눠줘 
---
굉장히 많이 쓰일 것 같은 함수 중에 하나.  
공백을 기준으로 문자열을 나눠줍니다.  
**공백이 아니라 다른 문자를 기준으로 자르고 싶다구요?** 그렇다면 `FieldsFunc()`를 사용하시면 됩니다.
### example code
```go
package main  
​  
import (  
    "fmt"  
    "strings"  
)  
​  
func main() {  
    fmt.Printf("Fields are: %q\n", strings.Fields("공백을 기준으로 나눠줍니다 공백이 여러개여도   나눠줍니다"))  
}  
​  
============================  
Output:  

Fields are: ["공백을" "기준으로" "나눠줍니다" "공백이" "여러개여도" "나눠줍니다"]
```


## FieldsFunc: 나는 공백말고 다른 기준으로 문자열을 나누고 싶어!
---
자바스크립트의 콜백 함수[^callback]가 떠오르는 방식이다.  
커스텀 함수를 설정하여 내가 원하는 기준으로 문자열을 나눌 수 있습니다.
### example code
```go
package main

import (
    "fmt"
    "strings"
    "unicode"
)

func main() {
// ,를 기준으로 문자열을 나눕니다
sepComma := func(r rune) bool {
    return r == ','
} 

// 알파벳, 숫자가 아닌 모든 문자를 기준으로 나눕니다
sep := func(r rune) bool {
    return !unicode.IsLetter(r) && !unicode.IsNumber(r)
}

    fmt.Printf("Fields are: %q\n", strings.FieldsFunc("a,b,c", sepComma))
    fmt.Printf("Fields are: %q\n", strings.FieldsFunc("이것도 / 나눠, 줄*래?", sep))
}
​  
============================  
Output:  

Fields are: ["a" "b" "c"]
Fields are: ["이것도" "나눠" "줄" "래"]
```

## Contains: 내가 찾는 문자열이 있어?
---
`substr`이 `s`안에 존재하는지 판별해줍니다.
### example code
```go
package main  
​  
import (  
    "fmt"  
    "strings"  
)  
​  
func main() {  
    fmt.Println(strings.Contains("byungwook son", "son"))  
    fmt.Println(strings.Contains("byungwook son", "SON"))  
    fmt.Println(strings.Contains("byungwook son", "none"))  
}  
​  
=============================  
Output:  

true  
false  
false
```


### time complexity?
```go
func Contains(s, substr string) bool {  
    return Index(s, substr) >= 0  
}
```

Index 함수 내부를 보면 아래와 같이 되어있습니다.  
간단히 요약하자면 kmp 알고리즘을 사용하는 `Index()`함수가 있고 이 함수는 `substr`이 `s`에 속할 때 그 시작 인덱스를 반환하며 속하지 않는다면 -1을 반환합니다.  
그래서 리턴 값이 -1이 아닌 정수라면 `true`를 반환, -1이라면 `false`를 반환해 주게 됩니다.

`Index()`
-   `if len(substr) == 0` --> 0
-   `if len(substr) == 1` --> 처음으로 속하는 인덱스
-   `if len(substr) == len(s)` --> 두 문자열이 같으면 0, 다르면 -1
-   `len(s)와 len(substr) 둘 다 충분히 작을 때` --> brute force로 찾음
-   `len(s), len(substr) 모두 충분히 클 때` --> KMP알고리즘 사용

따라서 `Contains()`의 **시간 복잡도**는 `O(n + k), n = len(s), k = len(substr)` = **`O(n)`**이 된다.  
안심하고 써도 되겠다!

```go
func Index(s, substr string) int {  
  n := len(substr)  
  switch {  
  case n == 0:  
    return 0  
  case n == 1:  
    return IndexByte(s, substr[0])  
  case n == len(s):  
    if substr == s {  
      return 0  
    }  
    return -1  
  case n > len(s):  
    return -1  
  case n <= bytealg.MaxLen:  
    // s가 16보다 작을 때는 브루트 포스로 하는게 더 빠름  
    if len(s) <= bytealg.MaxBruteForce {  
      return bytealg.IndexString(s, substr)  
    }  
    c0 := substr[0]  
    c1 := substr[1]  
    i := 0  
    t := len(s) - n + 1  
    fails := 0  
    for i < t {  
      if s[i] != c0 {  
        o := strings.IndexByte(s[i+1:t], c0)  
        if o < 0 {  
          return -1  
        }  
        i += o + 1  
      }  
      if s[i+1] == c1 && s[i:i+n] == substr {  
        return i  
      }  
      fails++  
      i++  
      if fails > bytealg.Cutover(i) {  
        r := bytealg.IndexString(s[i:], substr)  
        if r >= 0 {  
          return r + i  
        }  
        return -1  
      }  
    }  
    return -1  
  }  
  c0 := substr[0]  
  c1 := substr[1]  
  i := 0  
  t := len(s) - n + 1  
  fails := 0  
  for i < t {  
    if s[i] != c0 {  
      o := IndexByte(s[i+1:t], c0)  
      if o < 0 {  
        return -1  
      }  
      i += o + 1  
    }  
    if s[i+1] == c1 && s[i:i+n] == substr {  
      return i  
    }  
    i++  
    fails++  
    if fails >= 4+i>>4 && i < t {  
      j := bytealg.IndexRabinKarp(s[i:], substr)  
      if j < 0 {  
        return -1  
      }  
      return i + j  
    }  
  }  
  return -1  
}
```

## Join: 문자열을 합쳐줘
---
`Fields()`의 역연산(?)이다.  
이것도 굉장히 많이 쓰일 듯?? 사용법도 매우 간단하다.
### example code
```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	s := []string{"one", "two", "three"}
	fmt.Println(strings.Join(s, ", "))
}

======================
Output:
one, two, three

```

## Map: 모든 글자에 mapping을 거친 문자열을 반환해줘
---
문자열에 있는 한 글자마다 `mapping()`을 적용시킨 문자열을 반환해 줍니다.  
원본 문자열에 영향을 미치진 않습니다.

### example code
```go
package main
import (
	"fmt"
	"strings"
	"unicode"
)

func main() {
	s := "HeLLo STRINgs pAckaGE!"
	
	// 대문자를 전부 마스킹 해줍니다
	f := func(r rune) rune {
		if unicode.IsUpper(r) {
			return '*'
		}
		return r
	}
	fmt.Println("대문자를 마스킹해줘: ", strings.Map(f, s))
	fmt.Println(s)
}

=======================
Output:

대문자를 마스킹해줘:  *e**o *****gs p*cka**!
HeLLo STRINgs pAckaGE!
```

## Replace: 원하는 글자만 바꿔줘
---
문자열에서 특정 문자를 원하는 문자로 바꾸어주는 함수입니다.  
`n = -1`인 경우 `ReplaceAll()`과 같은 행동을 취합니다.  
실제 `ReplaceAll()`함수를 보면 이렇게 짜여있음ㅋㅋ
```go
func ReplaceAll(s, old, new string) string {
	return Replace(s, old, new, -1)
}
```
`Replace()`는 새로운 메모리를 할당하여 문자열을 만들어주기 때문에 원본에 영향을 미치지 않는다.
### example code
```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    pig := "oink oink oink"
    cow := strings.Replace(pig, "oink", "moo", -1)
    cow2 := strings.Replace(pig, "oink", "moo", 2)

    fmt.Println(pig)
    fmt.Println(cow)
    fmt.Println(cow2)
}

=========================
Output:

oink oink oink
moo moo moo
moo moo oink
```


## Split: `sep`으로 문자열을 나눠줘
---
`FieldsFunc()`와 비슷한 듯 다른 함수입니다.  
사용법은 매우 간단합니다.
### example code
```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    pig := "oink pig oink pig oink"

    fmt.Printf("%q\n", strings.Split(pig, "pig"))
}

======================
Output:

["oink " " oink " " oink"]
```


## Trim: 양쪽에서 `cutset`을 없애고 싶어
---
`trim`은 손질하다 라는 의미를 갖는 동사입니다.  
양쪽에서 `cutset`에 해당하는 문자를 삭제하여 반환해 줍니다.  
문자열의 양쪽에 존재하는 공백을 지울 때 유용할 듯합니다.  
자매품으로 `TrimLeft()`, `TrimRight()`와 같은 함수들도 있습니다.

### example code
```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    s := " Hello world "
    s2 := "trashtrash HELLO WORLD trashtrash"

    fmt.Println(strings.Trim(s, " "))
    fmt.Println(strings.Trim(s2, "tarhs "))
}

=========================
Output:

Hello world
HELLO WORLD
```


다음 글에서는 `Builder`와 `Reader`에 대해 알아보겠습니다.!

## Foot Note
---

[^deepcopy]: 깊은 복사(deepcopy): 실제 값을 메모리의 새로운 공간에 할당하는 행위를 말한다.
[^callback]: 콜백함수(callback): 다른 코드의 인수로서 넘겨주는 실행 가능한 코드를 말한다.
