---
title: What Is Hystrix? and How-it-Works [번역]
date: "2018-12-14T23:00:00.121Z"
layout: post
draft: false
path: "/posts/hystrix-basic"
category: "Dev"
description: "히스트릭스란 무엇인가? 어떻게 동작하는가"
---
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
![Hystrix command flow chart](./hystrix-command-flow-chart.png)

```
이미치 출처
https://github.com/Netflix/Hystrix/wiki/How-it-Works#isolation
```

####2. hystrix command

HystrixCommand 개체에만 적용 할 수 있으며 HystrixObservableCommand에는 사용할 수 없습니다.


- ```observe()``` - 의존관계의 응답을 나타내는 observable을 체크 하고 observable 소스를 복제해서 리턴을 합니다.
- ```toObservable()``` - observable이 체크될 때, Hystrix 명령을 실행하고 그 응답을 방출하는 Observable을 반환합니다
- HystrixCommand는 Observable implementation에 의해 뒷받침됩니다. 심지어 하나의 간단한 값을 반환하는 명령까지도 지원합니다.

####3. Is the Response Cached?

- request caching을 커맨드 사용할 수 있으며, 요청에 대한 응답을 캐시에서 사용할 수있는 경우 캐시된 응답은 Observable 형식으로 즉시 반환됩니다

####4. Is the Circuit Open?
- 커맨드가 실행 될 때, histrix는 서킷이 오픈되어 있는지 circuit-breaker를 통해 확인 합니다.
- 서킷이 오픈 되었을 때, histrix 커맨드는 실행이 되지 않지만 fallback의 흐름을 가져가게 됩니다.

####5. Is the Thread Pool/Queue/Semaphore Full?
- 명령과 관련된 스레드 풀 및 큐 (또는 스레드에서 실행 중이 아닌 경우 세마포)가 가득차면 histrix는 fallback.

####6. HystrixObservableCommand.construct() or HystrixCommand.run()
- run () 또는 construct () 메서드가 명령의 시간 초과 값을 초과하면 스레드가 TimeoutException을 throw합니다.
- 그 메소드가 취소 / 중단하지 않는 경우에, 최종 반환 값 run () 또는 construct () 메소드를 파기합니다.

####7. Calculate Circuit Health
- Hystrix는 통계를 계산하는 롤링 카운터 세트를 유지하는 circuit-breaker에 대한 성공, 실패, 거부 및 시간 초과를 보고합니다.

- 통계를 통해 circuit의 상태 'trip'을 결정합니다, 이 시점에서는 복구 기간이 경과 할 때까지 모든 후속 요청을 단락 시킵니다.


####8. Get the Fallback
- Hystrix는 command execute가 fail 즉, construct() or run()로 인해 예외처리가 될 때마다 fallback으로 돌아갑니다.
- 서킷이 오픈 되었을 때, 스레드풀이 가득 찼을 때 등 위의 이미지에 나와 있는 플로우 중에 fallback에 해당하는 경우들이 있습니다.

####9. Return the Successful Response
- Observable의 형태로 응답을 반환합니다

```
출처 - 문제가 될 시에 블로그에 포스팅된 글을 삭제 하겠습니다.
https://github.com/Netflix/Hystrix/wiki/How-it-Works#isolation
```