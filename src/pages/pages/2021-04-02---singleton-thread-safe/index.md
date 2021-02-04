---
title: 쓰레드 세이프한 싱글톤 (Thread-safe singleton pattern)
date: "2021-02-04T23:50:00.121Z"
layout: post
draft: false
path: "/posts/java/thread-safe-singleton-pattern"
category: "Dev"
description: "자바 쓰레드 세이프한, 멀티쓰레드 싱글톤 패턴"
---

#### What is singleton pattern?
싱글톤은 단 하나의 객체만을 생성하게 강제하는 패턴이다.
클래스를 통해 생성할 수 있는 객체는 Only One, 즉 한 개만 되도록 만드는 것이 싱글톤이다.


#### synchronized singletone
```java
    public class Singleton {
        private static Singleton instance;

        private Singleton() {

        }

        public static synchronized Singleton getInstance() {
            if (instance == null) {
                instance = new Singleton();
            }
            return instance;
        }
    }
```
쓰레드 동기화 문제의 가장 쉬운 해결방법은 synchronized키워드
단일 쓰레드가 대상 메소드를 호출시작~종료까지 다른 쓰레드가 접근하지 못하도록 lock 을 하기 때문에 
위와 같이 getInstance()메소드를 synchronized로 처리하면 멀티 쓰레드에서 동시 접근으로 인한 인스턴스 중복생성 문제는 해결


하지만, synchronized getinstance()의 경우 인스턴스를 리턴 받을 때마다 Thread동기화 때문에 불필요하게 lock이 걸리게 되어 비용 낭비가 크다. 실제로 고전적인 방식에서 인스턴스가 2개 이상 생성될 확률은 매우 적다. 또한 최초 instance초기화 문제 때문에 synchronized를 추가하였는데, 초기화가 완료된 시점 이후라면 synchronized는 불필요하게 lock을 잡을 뿐 별다른 역할을 하지 못한다


```
출처
https://javaplant.tistory.com/21
```