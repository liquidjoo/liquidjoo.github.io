---
title: ResourceServerConfigurer WebSecurityConfigurer diff
date: "2019-07-10T14:17:00.121Z"
layout: post
draft: false
path: "/posts/ResourceServerConfigurer-WebSecurityConfigurer-diff/"
category: "Dev"
description: "ResourceServerConfigurer WebSecurityConfigurer 차이점"
---

#### ResourceServerConfigurer WebSecurityConfigurer
인증 서버를 개발하면서 그냥 하면 되겠지라는 생각에 무심코 넘어가버린 녀석들이 있다. @EnableResourceServer, @EnableWebSecurity 이 녀석들이 주인공이다. 처음엔 @EnableWebSercurity 어노테이션과 WebSecurityConfigurerAdapter를 상속 받아서
```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .requestMatchers().antMatchers("/createOAuthUser")
                .and()
                .authorizeRequests()
                .antMatchers("/createOAuthUser").permitAll()
                .anyRequest()
                .authenticated();
    }
```
위와 같이 HttpSecurity에 대한 메소드를 오버라이드해서 사용했으나 OAuth 인증을 하려고 할 때 계속 해서 error code 401을 리턴해버린다.

#### 현재 문제 점
- 인증서버에서 자원을 써야한다. 401을 리턴한다.

- 처음엔 @Order(-1) 어노테이션을 컨피그 클래스에 설정하니 http로 접근 가능했으나 서블릿에 대한 순서를 조정해서 그런지 컨트롤러에서 아무것도 행하지 않는다. @RequestBody, HttpServlert 두 객체로 컨트롤러에서 받지 못하는 문제가 있었다.

- 이상하다고 느낀 것은 인증서버에 대한 예제 중에 간간히 @EnableResourceServer를 사용한 configure 클래스 파일들이 보였다. 물론 나의 생각이 잘못 되었다는 것을 금방 알 수 있었다. 난 WebSecurityConfiruer or ResourceServerConfirer 둘 중 하나만 사용하면 되는 줄 알았다. 두 클래스는 같이 HttpSecurity를 정의한 configure 메소드를 오버라이드 할 수 있어서 그렇게 생각을 했다.


#### @EnableResourceServer
- 클래스를 쫓아 주석을 보고 충격을 받았다.
```java
 Convenient annotation for OAuth2 Resource Servers, enabling a Spring Security filter that authenticates requests via
 an incoming OAuth2 token. Users should add this annotation and provide a <code>@Bean</code> of type
 {@link ResourceServerConfigurer} (e.g. via {@link ResourceServerConfigurerAdapter}) that specifies the details of the resource (URL paths and resource id). In order to use this filter you must {@link EnableWebSecurity
 &#064;EnableWebSecurity} somewhere in your application, either in the same place as you use this annotation, or somewhere else. 
 
 The annotation creates a {@link WebSecurityConfigurerAdapter} with a hard-coded {@link Order} (of 3). It's not
 possible to change the order right now owing to technical limitations in Spring, so you must avoid using order=3 in other WebSecurityConfigurerAdapters in your application (Spring Security will let you know if you forget).
 @author Dave Syer
 
```
- 간단히 보면 토큰을 통해 요청을 인증하는 Spring Security 필터를 사용하는 OAuth2 Resource Server에 대한 자원의 세부 정보를 제공
- 한번 더 줄이면.. 토큰을 통해 요청하는 자원서버에 접근할 때 설정할 수 있는 메소드를 제공 한다는 느낌? (어렵..-  _ -)
- 이미 ResourceServerConfigurer에서 WebSecurity를 상속한다
```java
@Configuration
public class ResourceServerConfiguration extends WebSecurityConfigurerAdapter implements Ordered {

	private int order = 3;
    ...
```
- 주의 할 점은 이미 order가 3이기에 order를 주게 되는 상황이면 이를 잘 피해서 활용 해야한다.

#### 결론?
- 이미 상속되어 있는 WebSecurity가 있기 때문에 충분히 ResourceServer로 개발이 가능하나, 인증 서버 처럼 별도로 auth 관련 security가 필요한 경우에는 별도로 config 클래스를 만들어서 WebSecurityConfigurerAdapter를 상속해 객체(bean)를 등록하면 된다..

```
출처 - 해당 클래스 내의 주석
```