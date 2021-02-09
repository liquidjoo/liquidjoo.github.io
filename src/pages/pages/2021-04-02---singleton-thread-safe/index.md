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


#### DCL(Double-Checked-Locking) Singleton 패턴
```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

위에 제시 된 synchronized 를 이용한 singleton 패턴의 문제를 한마디로 요약하면, 인스턴스 할당시점만 synchronized 처리되면 될 문제를 getInstance() 전체를 synchronized처리하여 성능문제를 야기한다는 점이다. 그래서 고안된 방법이DCL(Double-Checked-Locking) singleton패턴이다.

DCL singleton패턴은 getInstance() 내부에서 instance를 생성하는 경우만 부분적으로 synchronized 처리를 하여 생성과 획득을 분리한 획기적인 방법이다. 즉 인스턴스가 생성되어 있는지 확인해보고 인스턴스가 없는 경우 lock을 잡고 instance를 생성하는 방법이다.

그런데 여기에도 문제가 있다. 소스코드 논리적으로는 문제가 없지만 컴파일러에 따라서 재배치(reordering)문제를 야기한다.  위에 소스가 컴파일 되는 경우 인스턴스 생성은 아래와 같은 과정을 거치게 된다.


```java
public static Singleton getInstance() {

        if (instance == null) { // Thread B 수행

            synchronized (Singleton.class) {

                if (instance == null) {

                    // instance = new Singleton(); 아래와 같이 변환 됨

                    some_space = allocate space for Singleton object;

                    instance = some_space; // Thread A가 수행

                    create a real object in some_space; // 실제 오브젝트 할당
                }
            }
        }
        return instance;
    }
```

멀티스레드 환경일 경우 각 스레드마다 동일 메모리를 공유하는 것이 아닌 별도 메모리 공간(CPU캐시)에서 변수를 읽어온다. 이런 경우 각 스레드마다 동일한 변수의 값을 다르게 기억할 수 있다. 만약 Thread A가 인스턴스 생성을 위해서 instance = some_space;를 수행하는 순간 Thread B가 Singleton.getInstance()를 호출하게 되면 아직 실제로 인스턴스가 생성되지 않았지만, Thread B는 instance == null 의 결과가 false로 리턴되어 문제를 야기하게 된다.

#### volatile를 이용한 개선된 DCL
```java
public class Singleton {
        private volatile static Singleton instance;

        private Singleton() {
        }

        public static Singleton getInstance() {
            if (instance == null) {
                synchronized (Singleton.class) {
                    if (instance == null) {
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }
    }
```

volatile키워드를 이요하는 경우 instance는 CPU캐시에서 변수를 참조하지 않고 메인 메모리에서 변수를 참조한다.
그래서 위에서 초기에 제시된 DCL singleton패턴에서 reorder문제가 발생하지 않는다. 현재까지는 안정적이고 문제가 없는 방법으로 인정되고 있다. 
DCL singleton패턴을 사용한다면 반드시 volatile 접근제한자를 추가하여 주도록 하자.


```
출처
https://javaplant.tistory.com/21
```