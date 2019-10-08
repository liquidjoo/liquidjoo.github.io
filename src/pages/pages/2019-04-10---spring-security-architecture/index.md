---
title: 스프링 시큐리티 구조 (spring security architecture) of Spring Boot
date: "2019-10-04T18:12:00.121Z"
layout: post
draft: false
path: "/posts/spring-security-architecture"
category: "Dev"
description: "스프링 시큐리티 구조"
---

#### Spring Security Architecture
---
스프링 부트는 안전한 애플리케이션을 위해 기본 동작을 제공하기에 많이 추천 한다.  
모든 원리는 스프링 부트 뿐만아니라 모든 어플리케이션에 동일하게 적용 한다.  

#### Authentication (인증) and Access Control (접근 제어)
---
보안 애플리케이션은 두 가지 이상의 독립적인 문제들이 있다.  
- authentication (Who are you?)
- authorization (what are you allowed to do?) = access control
authentication 과 authorization은 헷갈릴 수도 있기 때문에 access control 으로 부르는 사람도 있다.  
스프링 시큐리티 구조는 authorization(인가)으로 부터 authentication(인증)이 분리 되어 있고, 둘 다 확장할 수 있는 전략을 가지고 있다.

#### Authentication (인증)
---
주요 전략은 ```AuthenticationManager``` 인터페이스를 사용해 하나의 메소드에서 구현하는 것 이다.
```java
public interface AuthenticationManager {

  Authentication authenticate(Authentication authentication)
    throws AuthenticationException;
}

```

AuthenticationManager의 가장 일반적인 구현은 AuthenticationProvider 인스턴스 체인에 위임한 ProviderManager 이다. 
AuthenticationProvider는 Authentication Type을 얻을 수 있는지 지원하는 쿼리를 제공

```java
public interface AuthenticationProvider {

	Authentication authenticate(Authentication authentication)
			throws AuthenticationException;

	boolean supports(Class<?> authentication);

}
```
ProviderManager는 같은 어플리케이션내에서 다양한 인증 메커니즘을 제공할 수 있다. 
그 방법은 AuthenticationProviders의 체인 구현을 통해 할 수 있다.

ProviderManager는 Optional로 모든 providers에서 null을 찾는다.
null이 발생하게 되면 Authenticaion의 결과는 AuthenticaionException(Runtime Exception)을 발생하게 한다.

어플리케이션은 자원 보호를 위해 로직을 그룹화를 할 수 있다.
각 그룹은 전용 AuthenticationManager 가질 수 있다.
종종 각 ProviderManager들은 부모를 공유를 하는데 글로벌 자원으로 모든 provider에 대해 fallback을 해준다.  
![authentication](./authentication.png)


#### Customizing Authentication Managers
---
AuthenticationManagerBuilder를 사용해서 in-memory, JDBC or LDAP 로 user details or custom UserDetailsService를 설정 할 수 있다.  
global(parent) AuthenticationManager configuring을 통한 예제
```java
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {

   ... // web stuff here

  @Autowired
  public void initialize(AuthenticationManagerBuilder builder, DataSource dataSource) {
    builder.jdbcAuthentication().dataSource(dataSource).withUser("dave")
      .password("secret").roles("USER");
  }

}
```



```
출처 https://spring.io/guides/topicals/spring-security-architecture
```