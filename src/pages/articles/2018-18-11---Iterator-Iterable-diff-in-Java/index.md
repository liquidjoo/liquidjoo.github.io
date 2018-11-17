---
title: Iterator, Iterable 차이점 [번역]
date: "2018-11-18T02:00:00.121Z"
layout: post
draft: false
path: "/posts/iterator-iterable/"
category: "Java Dev"
tags:
  - "iterator"
  - "iterable"
description: "Iterator, Iterable 차이를 알아보자"
---

Iterator 그리고 Iterable은 자바 컬렉션 인터페이스에서 제공을 하며 매우 비슷하고 종종 혼란을 주긴 하지만 Iterator, Iterable 다른 점이 있다.
만약 어떤 클래스에 iterable 인터페이스를 implements를 하면 iterator를 사용해 클래스를 반복 작업을 할 수 있는 능력이 생긴다.

1. iterable은 순회할 수 있는 컬렉션을 나타낸다. Iterable 인터페이스를 implement하면 객체는 for-each loop를 사용할 수 있게 해준다. (내부적으로 iterator() 메소드를 객체에 호출해서 가능)
아래의 예제는 컬렉션 인터페이스가 iterable 인터페이스를 상속하고 있음을 보여준다
```java
List <String> persons = new ArrayList<>(Arrays.asList("A", "B", "C"));
for (String person: persons) {
    System.out.println(person);
}
```

다른 한편으로는 Iterator 인터페이스는 다른 객체, 다른 종류의 컬렉션을 순회하게 해준다. Iterator를 사용해 순회하기 위해서는 hasNext() + next() 메소드를 사용할 수가 있다.
```java
Iterator <Integer> iterator = Arrays.asList(1, 2, 3, 4, 5).iterator();
while (iterator.hasNext()) {
            System.out.println(iiterator.next());
}
```

for-each loop에서 람다를 사용해서 Iterable 안의 Iterator로 컨버팅을 할 수 있다.
```java
for (Integer i: (Iterable<Interger>) () -> iterator) {
    System.out.println(i);
}
```

2. Iterable interface를 implements 하는 클래스는 iterator()를 오버라이드를 해야하고 iterable interface에서 메소드를 제공한다.
Iterator interface를 implements 하는 클래스는 hasNext(), next() 메소드를 오버라이드 해야한다. iterator interface에서 메소드를 제공한다.

3. Iterator instance는 iteration 상태를 모아둔 곳이다. 현재 element에 대해 다음 element가 존재하고 다음 element로 이동하는 유용한 메소드를 Iterator는 제공을 한다.
Iterator는 컬렉션 내의 현재 위치를 기억한다. 다른 한편인 Iterable은 어떠한 iteration 상태 값을 가지지 못한다.

4. Iterable은 iterator() 메소드가 호출이 될 때마다 iterator의 새로운 instance를 생성해야 한다.
이러한 이유는 Iterator 인스턴스는 iteration상태를 유지해야하고 구현이 같은 iterator를 두 번리턴하는 일이 없어야 하기 때문이다.

```
출처
https://www.techiedelight.com/differences-between-iterator-and-iterable-in-java/
```