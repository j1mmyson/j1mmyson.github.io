---
title: "[번역/요약] 협업을 위한 REST API Best Practices"
date: 2022-11-03 15:55:00 +0900
categories: [Development, Network]
tags: [cowork, restful, rest api]
---

> **먼저 이 글은 [REST API Best Practices – REST Endpoint Design Examples by Kolade Chris](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/) 아티클을 번역 및 요약한 것을 알립니다.**  
**이전 진행되던 팀 프로젝트에서 팀원들에게 도움이 되고자 노션에 번역, 요약해놨던 글을 거의 그대로 옮겨왔습니다.  
협업이 처음인데 RESTFUL함이 확실히 뭔지 모르겠다 하시는 분들이라면 아래의 규칙들만 잘 지켜도 꽤 좋은 API 설계를 할 수 있을 것이라고 생각합니다..!**


## 1. 데이터를 주고받을 때 Json을 사용하라!

## 2. API의 엔드포인트는 되도록 명사로, 동사 x!
REST API 는 요청에 이미 `GET`, `POST`, `PUT`, `PATCH`, `DELETE` 동사가 들어가 있기 때문에 
요청하는 엔드포인트에 `COPY`, `PURGE`, `LINK` 와 같은 동사는 넣지 말자.  

- 👍: `POST "/user"`  
- 👎: `POST "/createUser"`


## 3. 명사를 복수형으로 설계하라!
API의 엔드포인트가 `https:/mysite.com/post/123` 일때 `DELETE`, `PUT`, `PATCH` 메소드로 요청을 할때는 별 문제가 없다.  
하지만 `GET` 메소드로 호출했을 때 결과가 **하나 이상**일 수 있기 때문에 api 엔드포인트를 설계할 때 **복수형 명사를 사용**하여야 한다.
- 👍: `GET “/users/123”`
- 👎: `GET "/user/123"`

## 4. 에러를 핸들링 할때는 항상 `Status Code`를 사용하라!
사용자가 자신의 요청이 성공했는지, 실패했는지, 실패했다면 왜 실패했는지를 쉽게 알게 하기 위해 이미 정규화된 상태코드를 사용하자.  
**[상태코드는 아래 링크에서 확인]**  
[HTTP 상태 코드 - HTTP | MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)
    
## 5. 관계를 나타내기 위해 엔드포인트에 nesting을 사용하라!
때로는 서로 다른 엔드포인트가 연관된 작업을 수행하는데 그럴 때는 엔드포인트를 nesting(둥지틀기?)를 해주자.
- `특정 글쓴이의 글을 불러올때`
    - 👍: `GET "/post/{author}"`
    - 👎: `GET "/postAuthor?author={author}"`

- `특정 글의 댓글목록을 불러올때`
    - 👍: `GET "/post/{post_id}/comments"`
    - 👎: `GET "/comments/postid"`

> **단, 가독성과 멋을 해치지 않기위해 3번 초과의 nesting은 지양하자 !**

## 6. 데이터를 요청할때에는 필터링, 정렬, 페이징을 사용하라!
DB서버의 과부화를 막기위해 필요한 데이터만 요청할 수 있도록 `filtering`, `sorting`, `pagination`을 활용하자.
- `성별이 남자인 유저의 목록만 불러오고 싶을때`
    - 👍: `GET "/users?gender=male"`
    - 👎: `GET "/maleUser"`

## 7.  보안을 위해 SSL을 사용하라!
SSL을 사용하여 악의적인 해킹으로부터 덜 취약한 API를 만들자.
- 👍: `https://mysite.com`
- 👎: `http://mysite.com`

## 8. 버전관리를 하라!
너의 API에 새로운 업데이트 사항이 있다면, 그 업데이트로 이전 API를 전처럼 사용하지 못한다면 api를 수정하지말고 새로운 버전을 파야한다.  
그렇지 않으면 이전 api를 사용하던 어플리케이션이 제대로 작동하지 않게된다.  
대부분의 대기업, 개인들은 아래와 같은 방법으로 버전을 나눈다.
- `https://mysite.com/v1/user`: version 1
- `https://mysite.com/v2/user`: version 2

## 9. 정확한 API문서를 제공하라!
REST API를 만들때 너는 사용자가 API를 제대로 사용하도록 도와야한다.  
그 방법중 가장 좋은 방법은 **좋은 API 문서를 작성**하는 것이다.  
- 문서에 반드시 포함되어야 할 것
    - 적절한 API의 엔드포인트
    - 엔드포인트의 요청 예시
    - 여러개의 프로그래밍 언어로 구현하는 방법
    - 상태코드와 함께 나열된 여러개의 메시지 목록들

API 문서작성에 가장 많이 쓰이는 것은 `Swagger` 이며 `PostMan` 도 많이쓴다.