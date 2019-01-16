---
title: Instant Ribbon client initialization with Eureka server [번역 및 참고]
date: "2018-12-17T19:00:00.121Z"
layout: post
draft: false
path: "/posts/instant-ribbon-client-init-with-eureka/"
category: "Dev"
description: "instant-ribbon-client-init-with-eureka"
---
* 이 글은 원본의 글쓴이에게 번역 허락을 받고 쓴 글입니다. 저의 의견도 포함해서 적었습니다.

Netflix OSS를 사용하고 있는 Spring Cloud로 application architecture를 개발을 하고 있습니다.

#### [문제점] Wait until the Ribbon client is ready to serve 
기본적으로 Eureka 서버에서 서버 목록을 가져 오기 전에 30초 동안 기다리고 나서 리본의 로드 밸런서를 사용해서 특정 서비스로 라우팅을 할 수가 있습니다.

위와 같은 문제는 개발환경이 dev, local인 경우 빈번히 restart를 하는데 결과를 보려고 할 때마다 30초를 기다려야하는 고통이 따른다.

#### 기본적인 유레카 서버 configuration
```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }

}

```
```
server.port=8761
spring.application.name=eureka-server

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
eureka.client.service-url.defaultZone = http://127.0.0.1:8761/eureka
eureka.instance.ip-address=true
```

#### 그리고 client configuration
```
spring.application.name=appName
server.port=9999
eureka.instance.prefer-ip-address=true
eureka.client.service-url.defalutZone=http://127.0.0.1:8761/eureka
```

유레카 서버에 'appName'으로 등록이 되면 plain URL 대신 리본의 로드밸런스 RestTemplate와 appName (service name)으로 접근이 가능하다.
http://localhost -> http://appName으로 remote call이 가능하게 된다. 이렇게 유레카 서버에 등록된 서비스명으로 접근하기 위해선 최소 30초의 초기화시간이 필요하다.

#### log를 보자
```
logging:
  level:
    com:
      netflix:
        loadbalancer: debug
```

```
$ tail -f appName.log | grep "addServer\|clearing"
DEBUG 19080 --- [erListUpdater-0 c.netflix.loadbalancer.BaseLoadBalancer  : LoadBalancer [appName_defaultzone]:  addServer [192.168.0.14:9999]
```
 addServer 까지의 시간이 30초가 걸린다.

#### Compoents 관계

![ribbon-eureka-flow](./ribbon-eureka-flow.png)
1. 유레카 클라이언트는 유레카 서버에 등록하고 서버에 대한 health 체크를 30초 후에 한다. ```eureka.instance.leaseRenewalIntervalInSeconds``` 클라이언트에서 설정 할 수 있다.
2. 캐시가 invalidated 되기 전까지 유레카 서버는 기존 클라이언트에 대해 새로 등록하지 앟는다. 캐시 시간을 refresh하는 옵션은 서버에서 ```eureka.server.response-cache-update-interval-ms```로 설정할 수 있으며 기본 값은 30초 이다.
3. 리본을 사용할 때 밸런서는 유레카 를라이언트로 등록되어진 name으로 가져오는데, 유레카 클라이언트는 스스로의 로컬 캐시 이전에 유레카 서버로 부터 서비스를 가져오는 캐시를 사용하는데 이 refresh 옵션은 클라이언트에서 ```eureka.client.registryFetchIntervalSeconds``` 설정할 수 있다.
4. 어떠한 캐시가 만들어지기 전에 리본은 서버 리스트 캐시를 가지고 있다. 기본 값은 30초이며 클라이언트 사이드에서 ```ribbon.ServerListRefreshInterval``` or ```<clientName>.ribbon.ServerListRefreshInterval```으로 설정을 할 수가 있다.



```
참조
http://lifeinide.com/post/2017-12-07-instant-ribbon-client-init-with-eureka/
```
