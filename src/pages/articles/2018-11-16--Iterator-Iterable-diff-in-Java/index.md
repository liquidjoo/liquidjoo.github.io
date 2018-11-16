---
title: Perfecting the Art of Perfection
date: "2018-10-22T18:00:00.121Z"
layout: post
draft: false
path: "/posts/first-story/"
category: "Design Inspiration"
tags:
  - "story"
  - "mixberry"
description: "이야기 연습"
---

이야기 연습 중
믹스베리 스트로베리

```python
lang=python
```
Iterator 그리고 Iterable은 자바 컬렉션 인터페이스에서 제공을 하며 매우 비슷하고 종종 혼란을 주긴 하지만 Iterator, Iterable 다른 점이 있다.
만약 어떤 클래스에 iterable 인터페이스를 implements를 하면 iterator를 사용해 클래스를 반복 작업을 할 수 있는 능력이 생긴다.

1. iterable은 순회할 수 있는 컬렉션을 나타낸다. Iterable 인터페이스를 implement하면 객체는 for-each loop를 사용할 수 있게 해준다. (내부적으로 iterator() 메소드를 객체에 호출해서 가능)
아래의 예제는 컬렉션 인터페이스가 iterable 인터페이스를 상속하고 있음을 보여준다
```java
List <String> persons = new ArrayList<>(Arrays.asList("A", "B", "C"));
for (String person: persons) {
    System.out.println(person)
}
```

다른 한편으로는 Iterator 인터페이스는 다른 객체, 다른 종류의 컬렉션을 순회하게 해준다. Iterator를 사용해 순회하기 위해서는 hasNext() + next() 메소드를 사용할 수가 있다.
```java
Iterator<Integer> iterator = Arrays.asList(1, 2, 3, 4, 5).iterator();
while (iterator.hasNext()) {
            System.out.println(iiterator.next());
        }
```