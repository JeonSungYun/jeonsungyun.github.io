---
layout: post
title: "JVM과 DalvikVM 메모리 구조"
comments: true
description: "JVM과 DalvikVM 메모리 구조"
keywords: "jvm, dalvik"
---

[The Java® Virtual Machine Specification : Java SE 8 Edition](https://docs.oracle.com/javase/specs/jvms/se8/jvms8.pdf) 에 의하면, 

JVM의 런타임 가상 메모리 구조(데이터 영역)는 아래와 같이 6가지로 나뉘어져 있다. 각 영역은 JVM의 생명 주기를 따르거나, JVM이 관리하는 Thread 의 생명주기를 따른다.

### PC Register
> 각 Thread 마다 PC Register가 존재. 따라서, JVM은 동시에 (at once) 여러 Thread 가 실행되는 것을 지원함.
> 하나의 Thread는 하나의 Method ( run 등.. )에 대응.

### Java Virtual Machine Stacks ( = Java Stack )
> 각 Thread 마다 private Stack 이 존재.
> Method 호출에 의한 Frame 을 저장.
> 고정된 크기일 수 도 있고, 동적으로 늘리거나 줄일 수 있음.

### Heap
> 모든 JVM의 Thread 들에서 공유되는 영역. JVM 이 시작될 때 영역이 생성되고, 실행 중 garbage collector 에 의해 영역이 관리됨.
> 모든 Instance 들 또는 array 들이 할당된 메모리들이 존재하는 runtime 데이터 영역.

### Method Area
> 모든 JVM의 Thread 들에서 공유되는 영역.
> run-time constant pool, field, method data와 code, instance initialization, interface initialization 을 저장.
> Method area 는 JVM이 시작될 때 생성됨.

### Run-Time Constant Pool
> run-time constant pool 은 클래스 파일 당 한개 씩 존재하는 constant_pool table 의 런타임 표현.
> Method Area 에 할당되어있으나, JVM 에 의해 class나 interface가 create될 때에 run-time constant pool이 construct 된다. // 이해가 잘 안가는 부분..
> Native Method Stacks 
> JVM 내에 존재한다면, Native Method Stack 이 Thread 당 한개씩 존재.


요약하자면 아래 그림과 같이 표현할 수 있다.
![Picture1](/assets/images/jvm-summary.png)

그렇다면, JVM 과 Dalvik VM 은 어떻게 다른 것인가?

Dalvik VM 은 JVM 을 변형한 것이고, JVM 은 [Stack Machine](https://en.wikipedia.org/wiki/Stack_machine), Dalvik 은 [Register Machine](https://en.wikipedia.org/wiki/Register_machine) 이다.

주요 변형 목적은 mobile hardware 에 최적화 하기 위함이며, 이와 문맥을 같이하는 하드웨어적인 노력의 일환이 [Jazelle](https://en.wikipedia.org/wiki/Jazelle) 이다.