---
title: java protected default 차이
date: "2021-02-03T20:11:00.121Z"
layout: post
draft: false
path: "/posts/java/default-protected-diff
category: "Dev"
description: "자바 protected default 접근제어자"
---

#### What is access modifier?
접근제어자는 멤버 또는 클래스에 사용되어 해당하는 멤버 또는 클래스를 외부에서 접근하지 못하도록 제한하는 역할
접근 제어자를 사용하는 이유는 클래스의 내부에 선언된 데이터를 보호하기 위함
이것을 데이터 감추기라고 하며 객체지향개념에선 캡슐화(encapsulation)


#### default
- 접근제어자를 별도로 설정하지 않는다면 접근제어자가 없는 변수, 메소드는 default 접근제어자가 되어 해당 패키지 내에서만 접근이 가능

#### protected
- 접근제어자가 protected로 설정되었다면 protected가 붙은 변수, 메소드는 동일 패키지내의 클래스 또는 해당 클래스를 상속받은 외부 패키지의 클래스에서 접근이 가능

```
출처
https://wikidocs.net/232
https://88240.tistory.com/448
```