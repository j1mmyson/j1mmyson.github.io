---
title: "[짧] Query Parameter vs Path parameter"
date: 2022-06-27 14:15:00 +0900
categories: [Development, Shorts]
tags: [parameter, query parameter, path parameter]
---

## `Path params` vs `Query params`

우리는 서버의 라우팅을 설계할 때 아래와 같은 기능을 두가지 방법으로 나타낼 수 있다.

> 작성자가 bob인 모든 게시글들을 조회한다

1. `Query Params`

   ```
   /posts?id=bob
   ```

2. `Path Params`

   ```
   /posts/bob
   ```



여기서 1번과 같은 방식으로 데이터를 전송하는 것을 `query parameter` 2번을 `path parameter` 라고 한다.

이렇게 같은 기능을 두가지 형식으로 표현할 수 있는데 언제 어떤 형식을 사용해야 할까?  
그리 복잡하지 않은 구조라면 아래의 기준에따라 나눠보면 좋다.

> 어떤 리소스를 식별하고 싶다면 `Path Parameter`
>
> 정렬이나 필터링을 원한다면 `Query Parameter`

## 예시

- 아이디가 Jimmy인 유저를 가져온다. (특정 유저) => path params

```
[GET] /users/jimmy
```

- 직업이 프로그래머인 유저목록을 가져온다 (필터링) => query params

```
[GET] /users?job=programmer
```

- 게시글id값이 192인 게시글을 삭제한다. (특정 게시글) => path params

```
[DELETE] /posts/192
```

- 생성일을 기준으로 모든 유저목록을 불러온다. => query params

```
[GET] /users?sort=created_at
```

- 아이디가 jimmy인 유저의 게시글중(특정 유저) 에 카테고리가 게임인 게시글(필터링)을 불러온다.

```
[GET] /posts/jimmy?categori=game
```



