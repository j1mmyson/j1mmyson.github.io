---
title: "High Availability in REDIS (1) - Sentinel"
date: 2024-01-21 19:04:00 +0900
categories: [Development, DataBase]
tags: [redis, sentinel, HA]
---
**Redis**에서는 **Sentinel**, **Cluster** 구성을 지원하여 **고가용성**을 보장합니다.  
오늘은 고가용성이란 무엇인지, 레디스의 sentinel, cluster 구성에서는 어떻게 고가용성을 보장하는지에 대해 알아보겠습니다.

## 고가용성(High Availability)이란?
먼저 **가용성**이란 `uptime / (uptime+downtime)`  
즉, 서비스가 얼마나 장애없이 유저들에게 **지속적으로 제공**되었는지를 나타내는 지표입니다.  
그렇다면 가용성을 높이기 위해선 위 식의 `down time`을 0으로 수렴시켜야 하겠죠?  
서비스의 `down time`을 줄이기 위해서는 
1. 서비스가 다운되지 않도록 한다.
2. 서비스가 다운되었을 때 재빨리 복구한다.
요렇게 두 가지 전략을 떠올릴 수 있겠습니다.  

레디스에서의 고가용성은 2번 전략에 초점을 둡니다.  
레디스 노드가 다운되었을 때 **어떻게 장애를 감지하고 재빨리 서비스를 복구시킬 것인가!** 라고 할 수 있죠.  

레디스에서는 HA를 보장하기 위해 Sentinel 구성과 Cluster 구성을 제공합니다.  
두 구성의 특징이 뭔지, 어떤점이 다른지 알아봅시다.  
먼저 **Sentinel**구조 입니다.

## SENTINEL
Sentinel 구성은 어떻게 HA를 제공할까요?  
먼저 센티넬의 가장 일반적인 구조부터 살펴봅시다.

![sentinel](/assets/img/posts/database/sentinel.png){: .w-50 .shadow .rounded-10 w='699' h='400'}

> **Redis**는 **싱글 스레드로 동작**하기 때문에 여러 코어를 활용하지 못합니다.  
때문에 하나의 인스턴스에 두개 이상의 프로세스를 띄워 리소스를 좀 더 효과적으로 활용할 수 있습니다. 
{: .prompt-tip}

Sentinel의 사전적 의미는 **'감시자, 보초, 파수병'** 입니다.  
단어 뜻에서 유추가 가능하듯이 sentinel 구성은 **마스터 노드가 잘 살아있는지 감시**하는 **센티넬 노드**를 둡니다.  
센티넬 노드가 마스터 노드를 감시하고 있다가.. 마스터 노드가 다운된 것 같다 싶으면 마스터 노드의 replication, 즉 slave 노드를 마스터 노드로 승격시켜버리는거죠. 

승격 과정에 대해 좀 더 자세히 알아봅시다.  
아래는 센티넬 노드를 만들 때 작성하는 설정 파일입니다.  
<https://redis.io/docs/management/config/>

```
# sentinel.conf

...
sentinel down-after-milliseconds mymaster 5000
quorum 2
...
```

### 1. down-after-milliseconds  
master 노드가 살아있는지 확인하기 위한 설정값입니다.  
master 노드는 센티넬 노드에 주기적으로 ping을 보내게 되는데 이 설정값 이상동안 ping이 오지 않게되면 센티넬 노드는 "master가 죽었구나" 라고 판단하게 됩니다.

### 2. quorum  
위 구성에서는 3대의 센티넬 노드가 있었습니다.  
각 센티넬 노드에서는 주기적으로 master에 핑을 요청합니다.  
`down-after-milliseconds` 시간동안 ping이 master로부터 오지 않는다면 master가 down된 상태라고 판단합니다.  
이를 **SDOWN(Subjectively Down)**이라고 합니다.  

그렇게 3대의 센티넬 노드중 **quorum**(2)만큼의 노드가 주관적 다운을 선언하게되면 이는 **ODOWN(Objectively Down)** 상태로 인지하게되며 슬레이브 노드 중 하나의 노드를 마스터로 선정하여 승격시키는 **FailOver**작업이 수행됩니다.  

그래서 센티넬의 페일오버 전략을 간략히 정리하면 아래와 같습니다.  
- n대의 sentinel node가 **마스터를 감시**하며 주기적으로 ping을 보냅니다.
- master로 부터 `down-after-milliseconds`시간동안 응답이 오지 않는다면 **SDOWN**을 선언한다.
- `quorum`만큼의 센티넬 노드가 SDOWN을 선언하였다면 이는 **ODOWN** 상태로 간주되어 슬레이브 노드를 마스터 노드로 승격시킨다.


