---
layout: post
title: "Java static 키워드"
comments: true
description: "Java static 키워드"
keywords: "java, static"
---

Java 에서 static 키워드를 적용할 수 있는 곳은 3군데 이다.

### 클래스 (static inner class)
> Java 에서 nested class 타입 인스턴스(A)는 nesting class 타입 인스턴스(B) 레퍼런스를 암시적으로 가지고 있다. 만약, A 가 B 영역 밖에서 참조되고 있거나 A 에 대한 참조가 끊어지지 않은 상황이면, 연쇄적으로 B 에 대한 참조도 끊기지 않을 것이다. 
> 이것은 B 에 대한 참조가 더이상 필요하지 않으면서 A 에 의해 암시적으로 B 가 가비지 컬렉션되지 못한다는 것을 의미한다.
> 이것을 해결하려면 약한 참조(weak reference) 나 정적 내부 클래스(static inner class) 를 사용해야 한다.

### 멤버 변수
> 정적 멤버변수는 생성자가 호출되기 전에 초기화된다.
> 또한, 같은 타입의 객체 수준에서 메모리가 공유된다.

### 메서드
> 인스턴스 변수를 사용할 수 없는 범위에 있다. 즉, this 를 사용할 수 없다.
> 정적 메서드 내에서는 정적 멤버 변수/상수 외의 인스턴스 변수를 사용할 수 없다
> this 객체를 사용하지 않는 메서드는 정적 메서드로 정의하는 편이 좋다