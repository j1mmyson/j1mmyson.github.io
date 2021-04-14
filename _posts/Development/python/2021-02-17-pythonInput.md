---
title: "[Python] python에서 입력값을 받는 여러가지 방법"
date: 2021-02-17 +0800
categories: [Development, Python]
tags: [python, input]
---

# Python에서 입력값을 받는 여러가지 방법

> 알고리즘 문제를 풀 때 입력값을 받아야 하는 경우가 있다. (특히 백준)  
> 오늘 알고리즘 문제를 풀다가 기록해두고픈 방법 하나를 발견하였고 기존에 쓰던 방법과 함께 정리해보려 한다.

## 1. 하나의 값만 입력받을 때

입력값 n을 받아 구구단 n단을 출력하는 예제와 같이 하나의 입력값을 받는 경우다.

```bash
# input #
623
```

`input()`함수의 반환값은 기본적으로 문자열이다.
여러 코딩테스트 문제를 풀다보면 입력받은값을 `Int`타입으로 받아와야하는 경우가 많은데 `int()`함수로 간단하게 해결할 수 있다.

```python
n = input() # n = "623" (default = 문자열)
n = int(input()) # n = 623 (입력값을 int형 변수로 받고싶을 때)
```

`input()`함수 대신에 sys패키지의 함수를 사용해서 입력값을 받을 수도 있다.  
`sys`패키지의 함수를 사용해서 입력값을 받아오는 속도가 `input()`함수를 사용할 때보다 빠르다고 한다.

```python
import sys

n = sys.stdin.readline()
```



## 2. 한줄에 여러값을 받을 때

한줄에 여러값이 들어오고 공백을 기준으로 입력값을 분리하여 저장해야하는 경우다.

```bash
# input #
3 6 9 12 15
```

`split()`함수를 통해 분리가 가능하며 `split()`함수에 인자값을 넣어주지않으면 공백을 기준으로 나눈다. 공백이 아니라 `,`와 같은 특정값에 따라 분리해주고 싶다면 인자로 넣어주면 된다.   
`ex) input().split(',')`

```python
num_list = input().split()
num_list
# ["3", "6", "9", "12", "15"]
```

```python
import sys

num_list = sys.stdin.readline().split()
# ["3", "6", "9", "12", "15"]
```

한줄에 여러값을 `Int`타입으로 리스트에 저장하고 싶다면 `map()`함수를 사용한다.

```python
num_list = list(map(int, input().split()))
num_list
# [3, 6, 9, 12, 15]
```

```python
import sys

num_list = list(map(int, sys.stdin.readline().split()))
# [3, 6, 9, 12, 15]
```



## 3. 입력이 여러줄로 들어왔을 때

이 방법이 내가 기록하고싶었던 방법이다.    
문제를 풀고나서 다른사람의 풀이를 보다가 발견한 방법인데 어떠한 유형의 입력값이 들어와도 편하게 사용할 수 있을것 같았다.  
입력값을 한줄마다 나누어 저장하는 방법인데 앞서 보여준 두 방법과 결합하여 거의 모든 입력값에 대해 대응할 수 있겠다.   

```bash
# Input #
----------
10
byungwook
aaa
bbb
```

```python
import sys
data = sys.stdin.read().splitlines()
# data[0] = "10"
# data[1] = "byungwook"
# data[2] = "aaa"
# data[3] = "bbb"
# 와 같이 한줄씩 잘라서 리스트에 저장됨.
```

`sys.stdin.read().splitlines()`로 입력값을 받아오는 코드를 내 터미널에서 테스트해보고 싶을 때, 입력값의 끝을 알려주어야 하는데 이 신호를 EOF(End Of File)라 하며 윈도우에서는 `Ctrl + z` 리눅스, 맥에서는 `Ctrl + d`를 입력해 입력의 끝을 알릴 수 있다.                                                                                                     