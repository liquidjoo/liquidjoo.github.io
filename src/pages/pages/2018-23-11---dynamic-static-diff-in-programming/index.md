---
title: 동적 정적 프로그래밍 언어에서의 정리
date: "2018-11-23T02:00:00.121Z"
layout: post
draft: false
path: "/posts/dynamic-static"
category: "Dev"
tags:
  - "dynamic"
  - "static"
  - "dispatch"
description: "프로그래밍 언어에서 동적과 정적을 알아보자"
---

##### 동적 Dynamic
- '동적'이라는 말은 실행 시점을 의미

##### 정적 Static
- '정적'이라는 말은 컴파일 시점을 의미

실행 시점에 객체 타입에 따라 동적으로 호출될 대상 메소드를 결정하는 방식을 동적 디스패치(dynamic dispatch)라고 한다. 반면 컴파일 시점에 알려진 변수 타입에 따라 정해진 메소드를 호출하는 방식은 정적 디스패치(static dispatch)라고 한다.

```
출처 - Kotlin in Action [번역본]
```