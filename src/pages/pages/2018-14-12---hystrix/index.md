---
title: What Is Hystrix? and How-it-Works [번역]
date: "2018-12-14T23:00:00.121Z"
layout: post
draft: false
path: "/posts/hystrix"
category: "Dev"
description: ""
---

##### 


![Hystrix command flow chart](./hystrix-command-flow-chart.png)

```
이미치 출처
https://github.com/Netflix/Hystrix/wiki/How-it-Works#isolation
```


####What Is Hystrix?
- In a distributed environment, inevitably some of the many service dependencies will fail. Hystrix is a library that helps you control the interactions between these distributed services by adding latency tolerance and fault tolerance logic. Hystrix does this by isolating points of access between the services, stopping cascading failures across them, and providing fallback options, all of which improve your system’s overall resiliency.

####간단히..해서
- 분산 서비스간의 상호작용을 제어하는데 도움이 되는 라이브러리
- 장애 내성(fault tolerance)과 지연 내성(latency tolerance)
- 서비스간의 액세스 지점을 격리하고 이를 통해 계단식 오류를 중지하고 fallback이라는 옵션들을 제공한다. 이 모든 것이 시스템의 전반적인 전반적인 복원력을 향상시킨다.
```
출처
https://github.com/Netflix/Hystrix/wiki
```


###How-it-Works

