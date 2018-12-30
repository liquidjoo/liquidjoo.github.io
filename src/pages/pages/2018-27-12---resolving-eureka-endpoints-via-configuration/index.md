---
title: resolving eureka endpoints via configuration
date: "2018-12-27T19:00:00.121Z"
layout: post
draft: false
path: "/posts/resolving-eureka-endpoints-via-configuration/"
category: "Dev"
description: "why every 5 minutes to print log 'resolving-eureka-endpoints-via-configuration'? "
---

### Heartbeat?

eureka를 사용하면서 client의 기능 테스트를 하면서 로그를 보다가 매번 찍히는 로그가 있었다.
```resolving-eureka-endpoints-via-configuration``` 이런 로그가 5분 단위로 찍히는데 
```
2018:12:27 08:44:03.815 INFO  --- [AsyncResolver-bootstrap-executor-0] c.n.d.s.r.aws.ConfigClusterResolver : Resolving eureka endpoints via configuration
2018:12:27 08:49:03.816 INFO  --- [AsyncResolver-bootstrap-executor-0] c.n.d.s.r.aws.ConfigClusterResolver : Resolving eureka endpoints via configuration
2018:12:27 08:54:03.817 INFO  --- [AsyncResolver-bootstrap-executor-0] c.n.d.s.r.aws.ConfigClusterResolver : Resolving eureka endpoints via configuration
```
이러한 로그들은 client가 eureka server로 보내는 heartbeat이다. 즉, eureka server에 대한 헬스 체크를 cilent단에서 하는 것이다. 짐작은하고 있었지만 확인을 하기위해서 찾아봤다..
 heartbeat단위를 수정할 수도 있는데 
 ```
 application.properties 파일 내에
 eureka.instance.leaseRenewalIntervalInSeconds=5
 ```
 위와 같은 방법으로 설정을 할 수가 있다.

```
참조
https://github.com/spring-cloud/spring-cloud-netflix/issues/2038
```