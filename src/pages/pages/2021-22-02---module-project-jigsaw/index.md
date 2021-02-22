---
title: project jigsaw module
date: "2021-02-22T20:12:00.121Z"
layout: post
draft: false
path: "/posts/java/module-project-jigsaw"
category: "Dev"
description: "java 9 ~ 프로젝트 직소에서 모듈이란?"
---

#### What is jigsaw project?



##### module
module-info.java (모듈 선언 파일)에 `이름이 무엇인가? (name)`, `어떤것을 제공하는가? (export)`, `어떤것들이 필요한가? (require)` 의 답을 선언하는 소프트웨어적인 단위

##### 이름이 무엇인가? (name)
- 각 Module에는 이름이 존재
- 이름은 충돌을 피하기 위해 패키지 명명 규칙과 유사 (java.bbb.abcd)

##### 어떤것을 제공하는가? (export)
- Module은 다른 외부에서 사용할 수 있도록 공개 API로 간주 되는 모든 패키지 목록을 제공
- 어떤 클래스가 public 이라 할지라도 export 된 패키지에 없으면 모듈 외부의 어떤것도 이 클래스에 접근 할 수 없음
##### 어떤것들이 필요한가? (require)
- 우리 모듈은 다른 모듈의 export된 모든 public type에 접근 가능
- Jigsaw 팀은 이것을 다른 모듈 읽기 (reading another module) 라고 함


#### 기존과 다른점
- Java 8 까지 classpath에 있는 모든 public type은 다른 어떤 type에서도 접근이 가능
- Jigsaw를 사용하면 Java의 type들에 대한 기존의 접근 방법이 바뀜
##### 기존
```
public
private
default (같은 패키지)
protected (같은 패키지 + 상속)
```
##### 변경
```
외부에 모두 public (public to everyone who reads this module (exports))
특정 모듈에만 public (public to some modules that read this module (exports to))
모듈 내부에만 public (public to every other class within the module itself)
private
default
protected
```

#### JDK 모듈화
- 모듈의 종속성은 순환 종속성(상호 참조)을 금지하는 비순환 그래프를 형성
- 비순환을 위해 Jigsaw 팀의 주요 작업 중 하나는 기존 Java Runtime의 순환 참조 및 직관적이지 않은 종속성을 모듈화
- 그래프 하단의 `java.base` 가 존재 (java.lang.Ojbect 와 유사) → 생성하는 모든 모듈이 모듈 내부의 선언 여부에 관계없이 `java.base`를 참조
- JDK의 모듈화는 사용하려는 Java Runtime의 모듈을 따로 지정할 수 있음


```
출처
https://infoscis.github.io/2017/03/24/First-steps-with-java9-and-jigsaw-part-1/ 
https://openjdk.java.net/jeps/200
```