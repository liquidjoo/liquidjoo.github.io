---
title: 서비스에 스프링 클라우드 (MSA) 적용 후기
date: "2019-02-24T21:27:00.121Z"
layout: post
draft: false
path: "/posts/review-spring-cloud/"
category: "Dev"
description: "용어들 정리"
---

#### 운영 중인 서비스에 spring cloud 적용기
현재 spring boot 버전 1.4로 운영중인 서비스가 있는데 QA를 진행하고 추가적인 개발, 배포(jar파일을 업로드 재시작 시에 서비스 이용 불가)를 할 때마다 불편함을 느끼고 있었는데 netflix 관련하여 T아카데이 세미나를 듣고 온 후에 독립적으로 서비스 될 수 있는 부분들을 찾아서 분리를 시작했다.

#### 현재 문제 점
- 현 프로젝트는 멀티 프로젝트로 하나의 마스터 아래에 여러 모듈(프로젝트)이 존재
- 클라우드를 사용하기엔 spring boot의 낮은 버전

- 각 모듈간의 의존성이 너무 큼

- deploy시 전체 서비스 이용 불가

- 기능이 추가 및 수정할 때마다 전체 deploy

- ..etc

#### 일단 하나씩..
- 4개의 서브 모듈이 존재하는데 의존성이 너무 커서 model 부터 다시 분리 (JPA 사용) mybatis를 사용안하고 있어서 정말 다행이라고..생각

- db 분리는 천천히.. (세미나에서 db 분리가 꽃..이라고 Hellgate Open)


#### 고군분투
- 우선 서비스 되고 있는 전체 로직을 파악 (적고, 흐름 그리고..)

- 새로운 프로젝트로 생성 (기존 서비스에서 부분적으로 변경)

- 프론트가 필요한 api 작업은 협업..

- 다른 개발 및 QA도 있기에 시간을 쪼개고, 일과 시간 외에 투자!!


#### 시작
- spring boot 2.1 버전 UP!!

- 우선 첫 개발 구성은 애플리케이션에선 hystrix, ribbon, eureka-client, Feign을 사용, Eureka-Server, Zuul Server를 통해 로드밸런싱, 라우팅, 보안, 등의 처리를 할 예정

- MSA로 개발 (hystrix로 인해 각 함수별 설정값이 필요..)

- 현재 애플리케이션의 모든 통신은 API 통신

- 일단 시작

#### 현재 dependencies 
```
dependencies {
    implementation('org.springframework.retry:spring-retry:1.2.2.RELEASE')  // spring cloud requires spring-retry for auto-retry
    implementation('org.springframework.cloud:spring-cloud-starter-openfeign')  // To use Feign
//    implementation('org.springframework.cloud:spring-cloud-starter-netflix-eureka-client') // 3. To use Eureka client
//    implementation('org.springframework.cloud:spring-cloud-starter-netflix-ribbon')  // 2. To use ribbon
    implementation('org.springframework.cloud:spring-cloud-starter-netflix-hystrix') // 1. To use spring-cloud-hystrix
    implementation('org.springframework.boot:spring-boot-starter-data-jpa')
    implementation('org.springframework.boot:spring-boot-starter-jdbc')
    implementation('org.springframework.boot:spring-boot-starter-web')
    implementation('org.springframework.boot:spring-boot-starter-actuator')
    implementation('com.squareup.okhttp3:okhttp:3.9.1')
    compileOnly('org.projectlombok:lombok')
    compile group: 'com.google.code.gson', name: 'gson', version: '2.6.2'
    testImplementation('org.springframework.boot:spring-boot-starter-test')
    compile('mysql:mysql-connector-java')
}

```
- 유레카랑 리본은 아직 오버 스펙이라 생각이 들기에 테스트만 해보고 실질적으로 도입은 하지 않는 걸로 결정!!

- 유레카를 쓰려면 유레카 서버를 또 관리해야하기 때문에 ㅠ 생각보다 많은 자원을 먹을 것 같아서.. 포기

#### 개발
- 스케줄링은 별 다른 기능은 없이 순수 task 작업으로 이루어져 있음.

- 동시에 작업을 위해 멀티쓰레드 사용 (ExecutorService)
```java
ExecutorService executorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());
```

- 리턴 값이 필요했기에 Runnable 보단 Callable 사용

- 리스트에서 데이터를 가져가는데 있어서 중복된 값을 막기 위해 LinkedBlockingQueue 사용

- 스케줄링 작업을 위한 데이터는 FeignClient를 통해 Api Call

- Hystrix를 사용해 fallback 조건 조정
```
hystrix.command.Service#function(params).execution.isolation.thread.timeoutInMilliseconds=2000
hystrix.command.Service#function(params).circuitBreaker.requestVolumeThreshold=20
hystrix.command.Service#function(params).errorThresholdPercentage=50
```

- FeignClient내의 fallbackFactory를 사용해 fallback 리턴 값 조정
```java
    @Override
    public String create(Throwable cause) {
        System.out.println("t=" + cause);
        return "cause: " + cause;
    }

```



```
출처 - 
```