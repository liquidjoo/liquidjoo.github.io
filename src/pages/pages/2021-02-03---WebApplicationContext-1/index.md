---
title: WebApplicationContext
date: "2021-03-02T23:09:00.121Z"
layout: post
draft: false
path: "/posts/java/spring/web-application-context-1"
category: "Dev"
description: "Web Application Context 1"
---

#### What is Web Application Context?



##### Web Application Context 동작 방식
웹 애플리케이션은 동작하는 방식이 근본적으로 다르다.
독립된 자바 프로그램은 자바 VM에 main() 메소드를 가진 클래스를 시작 시켜 달라고 요청할 수 있지만
웹에는 main() 메소드를 호출할 방법이 없다 게다가 사용자도 여럿이며 동시에 웹 애플리케이션을 사용한다.
그래서 웹 환경에서는 main() 메소드 대신 서블릿 컨테이너가 브라우저로 부터오는 
HTTP 요청을 받아서 해당 요청에 매핑되어 있는 서블릿을 실행해주는 방식으로 동작한다.
서블릿이 일종의 main() 메소드와 같은 역할을 하는 셈이다.
일단 main() 메소드 역할을 하는 서블릿을 만들어두고 미리 애플리케이션 컨텍스트를 생성해둔 다음
요청이 서블릿으로 들어올 때마다 getBean()으로 필요한 빈을 가져와 정해진 메소드를 실행해주면 된다.




```
출처
토비의 스프링 3.1 v2

저작권 문제가 있을 시 해당 글을 삭제하도록 하겠습니다.
```