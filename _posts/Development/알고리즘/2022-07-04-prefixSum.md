---
title: "[알고리즘/짧] 구간 합 알고리즘, Prefix Sum"
date: 2022-07-04 15:15:00 +0900
categories: [Development, Algorithm]
tags: [prefix sum, algorithm]
---

## 내가 구하고 싶은 것

배열이 주어졌을 때 내가 원하는 구간의 원소들의 합.

```python
arr = [1, 3, 5, 7, 9, 12]
```

```python
def prefixSum(arr, start, end):
  # arr배열의 start번째 원소부터 end번째 원소까지의 합
  return result
```

## 비효율 적인 방법

```python
def prefixSum(arr, start, end):
  result = 0
  for i in range(start, end):
    result += arr[i]
  
  return result
```

## 효율적인 방법

2부터 5 구간 사이의 합은 `5 + 7 + 9 + 12 = 33`이다.  
이는 5번째 원소까지의 합 `1 + 3 + 5 + 7 + 9 + 12` 에서 (2-1)번째 원소까지의 합 `1 + 3` 을 뺀 값과 같다.

```python
arr = [1, 3, 5, 7, 9, 12]
arrSum = [0]

for v in arr:
  arrSum.append(v+arrSum[-1])
# arrSum = [0, 1, 4, 9, 16, 25, 37]

def prefixSum(arrSum, start, end):
  result = arrSum[end+1] - arrSum[start]
  return result
```

## 왜 효율적인가

하나의 배열이 주어지고 구간 합의 구간이 k 개 주어졌을 때를 가정해 봅시다.  
비효율적인 방법을 사용하면 **k 개의 쿼리를 받을 때마다 구간 사이의 거리에 따라 다르겠지만 배열을 순회하며 구간 합을 구해야** 합니다. 이때 **시간 복잡도는 k * O(n) (이때 n은 배열의 길이) = O(n)**입니다.  

하지만 두 번째 방법을 사용하게 되면 처음에 `arrSum`을 한번 구해주기만 하면 **이후에 들어오는 모든 요청들에 대해서 배열의 두 값에 접근하여 차잇값만 구하면** 되므로 O(1) 만큼의 시간밖에 소요되지 않습니다. 따라서 이 방법의 **시간 복잡도는 처음에 배열을 구하는 시간 n + k * O(1) = O(1)**이 됩니다.  

## 결론

> 들어오는 구간합을 구하는 요청이 많아질수록, 배열의 길이가 길어질수록  
두 번째 방법이 유리한 접근 방법이 된다.
{: .prompt-tip }
