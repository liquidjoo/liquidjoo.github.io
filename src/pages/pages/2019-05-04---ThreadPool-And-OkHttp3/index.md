---
title: 멀티쓰레드와 비동기 통신 사용 후기
date: "2019-04-05T19:27:00.121Z"
layout: post
draft: false
path: "/posts/review-threadpool-and-okhttp3/"
category: "Dev"
description: "비동기 처리를 위해 멀티쓰레드만 사용한 필자의 후기"
---

#### 멀티쓰레드가 이상하다?
이전에 spring cloud를 사용해 특정 작업을 스케줄링하는 개발을 했었는데 같은 로직으로 HttpConnection을 맺으니 갑자기 속도가 느려지며 GC Overhead가 발생!!

#### 문제가 뭐지..
- 가비지 컬렉션 오버헤드가 발생해서 당연히 메모리 부족인 줄.. jvm의 메모리를 올려봤으나 차이가 없다

- 로직의 문제인가 싶어서 ```synchronized ()``` 를 알아봤으나 이 문제는 아니라고 판단 (데이터 자원을 보존하기 위한 보호가 로직에 필요 없다고 판단!)

#### 찾자 일단 하나씩..
- 한줄 한줄 디버깅해가면서 찾아보니 HttpURLConnection 요놈에서 뭔가 문제가 있는 듯 하다!

```java
public String callApi(String query, String method) {
        String response = null;
        HttpURLConnection con = null;
        try {
            URL url = new URL(query);
            con = (HttpURLConnection) url.openConnection();
            con.setRequestMethod(method);
            con.setRequestProperty("X-Requested-With", "Curl");

            InputStreamReader inputStreamReader = new InputStreamReader(con.getInputStream());
            BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
            ...
            ....

            inputStreamReader.close();
            bufferedReader.close();

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (con != null) {
                con.disconnect();
            }
        }
```

- 외부 Api호출에 있어서 단순히 HttpUrlConnection 객체를 사용하고 있었는데!! 이 친구에서 계속 뻑이 난다

#### 고군분투
- 자원해제, 커넥션 객체 할당 해제를 잘 해준 것 같은데도 어느정도 시간이 지나면 GC OverHead 

- 멀티쓰레드를 사용하고 있고 슬립 시간이 거의 없이 계속 많은 양의 데이터를 받는데 할당하고 해제를 계속 하다가 뻑이 나는건지..사실 아직 잘 모르겠다.

- 현재 느낀점은 들어오는 데이터 양에 비해 생각보다 많은 양의 커넥션을 맺고 있고 해제하는 속도보다 객체를 할당하는 부분이 더 많아서 GC(가비지 컬렉션)에서 에러를 뿜는 것 같다.


#### 그럼?
- 코드의 메모리 누수(memory leaks)를 찾아야 한다.

- 우선 다른 일정도 많이 밀려있기에 HttpURLCollection 객체를 사용해서 만든 로직을 폐기..

- 로직이 멀티쓰레드로 동작한다면 로직내의 Http 통신 조차 비동기로 하면 된다고 생각!

- okhttp3 라이브러리 사용!! (비동기 통신)

- 음.. github에서 오픈소스인 facebook sdk를 뜯어서 필요한 것만 추출해서 개발

- 일단 카피카피! 시작

#### 새롭게 만들어진 로직?
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