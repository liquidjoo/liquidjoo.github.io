---
title: 싱글톤 패턴 (singleton pattern)
date: "2021-02-03T20:45:00.121Z"
layout: post
draft: false
path: "/posts/java/singleton-pattern
category: "Dev"
description: "자바 싱글톤 패턴"
---

#### What is singleton pattern?
싱글톤은 단 하나의 객체만을 생성하게 강제하는 패턴이다.
클래스를 통해 생성할 수 있는 객체는 Only One, 즉 한 개만 되도록 만드는 것이 싱글톤이다.


#### 실패하는 경우
```java
class Singleton {
    private Singleton() {
    }
}

public class SingletonTest {
    public static void main(String[] args) {
        Singleton singleton = new Singleton();
    }
}
```
위와 같은 코드를 작성하면 컴파일 에러가 발생
왜냐하면 Singleton 클래스의 생성자에 private 키워드로 외부 클래스에서 Singleton 클래스의 생성자로의 접근을 막았기 때문
이렇게 생성자를 private 으로 만들어 버리면 외부 클래스에서 Singleton 클래스를 new 를 이용하여 생성할 수 없게 된다.
new를 이용하여 무수히 많은 객체를 생성한다면 싱글톤의 정의에 어긋나지 않겠는가? 그래서 일단 new로 객체를 생성할 수 없도록 막은 것이다.

#### 싱글톤은 아님
```java
class Singleton {
    private Singleton() {
    }

    public static Singleton getInstance() {
        return new Singleton();
    }
}

public class SingletonTest {
    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
    }
}
```
위와 같이 코드를 변경하면 이제 getInstance라는 static 메소드를 이용하여 Singleton 객체를 돌려 받을 수 있다. 
하지만 getInstance를 호출 할 때마다 새로운 객체가 생성되게 된다. 그렇다면 싱글톤이 아니다.

#### 싱글톤 (non thread safe)
```java
class Singleton {
    private static Singleton one;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if(one == null) {
            one = new Singleton();
        }
        return one;
    }
}

public class SingletonTest {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();
        System.out.println(singleton1 == singleton2);
    }
}
```

Singleton 클래스에 one 이라는 static 변수를 두고 getInstance 메소드에서 one 값이 null 인 경우에만 객체를 생성하도록 하여 one 객체가 단 한번만 만들어지도록 했다.
####getInstance 메소드의 동작원리를 살펴보자.
- 최초 getInstance가 호출 되면 one이 null이므로 new에 의해서 객체가 생성
- 이렇게 한번 생성이 되면 one은 static 변수이기 때문에 그 이후로는 null이 아니게 된다.
- 그런 후에 다시 getInstance 메소드가 호출되면 이제 one은 null이 아니므로 이미 만들어진 싱글톤 객체인 one을 항상 리턴하게 된다.

main 메소드에서 getInstance를 두번 호출하여 각각 얻은 객체가 같은 객체인지 조사 해 보았다. 역시 예상대로 "true"가 출력되어 같은 객체임을 확인 할 수 있다. (객체의 동일성)
싱글톤 패턴은 static에 대한 이해만 있다면 참 알기쉬운 패턴 중 하나


```
출처
https://wikidocs.net/228
```