---
title: "Java 메모리 구조 완전 정리"
date: 2025-12-05
categories: Java
tags: [Java, Memory, JVM, 스택, 힙]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

# Java 메모리 구조 완전 정리 – 면접 대비 필수 개념

자바(Java)를 제대로 이해하기 위해 반드시 알야아 하는 개념이 **JVM 메모리 구조(Java Memory Model)**입니다.  
이 구조를 이해하면 객체 생성, 데이터 저장 위치, GC 동작 방식, 변수 활용 방식까지 자연스럽게 이해할 수 있습니다.  
실제 면접에서도 매우 자주 등장하는 핵심 주제입니다.

---

# 🔥 1. JVM(Java Virtual Machine) 메모리 구조란?

JVM은 프로그램 실행을 위해 메모리를 아래와 같이 구분합니다:

- **Method Area (메타데이터 & static 저장)**
- **Heap (객체 저장 공간)**
- **Stack (지역 변수 & 메서드 호출 스택)**
- PC Register  
- Native Method Stack

아래 그림은 JVM 메모리 구조의 전체 흐름을 보여줍니다.

![JVM Memory Overview](/assets/images/java/JVM-Architecture-diagram.jpg)

---

# 🧩 2. JVM 메모리 영역 상세 설명

## ① Method Area (메서드 영역)

JVM 실행 시 로딩되며, 클래스 관련 정보가 저장되는 공간입니다.

### 저장되는 데이터
- 클래스 메타데이터
- static 변수
- static 메서드
- final 상수
- 메서드 바이트코드

### 특징
- 프로그램 종료까지 유지됨  
- 모든 스레드가 공유  


---

## ② Heap (힙 영역)

`new` 키워드로 생성되는 **모든 객체가 저장되는 공간**입니다.  
GC(가비지 컬렉션)의 주요 대상이 바로 힙입니다.

### 저장되는 데이터
- 객체 인스턴스
- 배열
- 인스턴스 변수

### 특징
- 스레드 간 공유됨  
- GC가 사용되지 않는 객체를 정리  


---

## ③ Stack (스택 영역)

메서드가 호출될 때마다 **스택 프레임(Stack Frame)**이 쌓이고,  
메서드 실행이 끝나면 해당 프레임이 즉시 제거됩니다.

### 저장되는 데이터
- 지역 변수
- 매개변수
- 기본 타입 값
- 참조 변수의 주소값(reference)
- 리턴 주소

### 특징
- LIFO(Last-In First-Out) 구조  
- GC의 대상이 아님  


---

# 🧠 3. 기본 타입(Primitive Type) vs 참조 타입(Reference Type)

자바 변수는 크게 두 가지로 나뉩니다:

- **기본 타입(Primitive Type): 값 자체 저장**
- **참조 타입(Reference Type): 주소값(reference) 저장**


![Stack Frame Diagram](/assets/images/java/java-heap-stack-diagram.png)


---

## ✔ 기본 타입(Primitive Type) — 총 8개

기본 타입은 **스택(Stack)에 값 자체가 저장**되며, 크기와 범위가 고정되어 있어 성능이 매우 빠릅니다.

### 기본 타입 표 (크기 + 범위 + 예시)

| 타입 | 크기 | 저장 형태 | 범위 | 예시 |
|------|------|-----------|-----------------------------|-------------|
| **byte** | 1 byte | 정수 | -128 ~ 127 | `byte b = 10;` |
| **short** | 2 byte | 정수 | -32,768 ~ 32,767 | `short s = 1000;` |
| **int** | 4 byte | 정수(기본) | 약 ±2.1 billion | `int i = 100000;` |
| **long** | 8 byte | 정수 | 매우 큼 | `long l = 10L;` |
| **float** | 4 byte | 실수 | ±3.4e−38 ~ ±3.4e38 | `float f = 3.14f;` |
| **double** | 8 byte | 실수(기본) | ±1.7e−308 ~ ±1.7e308 | `double d = 3.14;` |
| **char** | 2 byte | 유니코드 문자 | 0 ~ 65,535 | `'A'`, `'가'` |
| **boolean** | 1byte | 논리값 | true / false | `boolean flag = true;` |

### boolean의 크기가 왜 “JVM 의존”인가?

자바 스펙에서는 boolean의 정확한 바이트 크기를 정의하지 않았기 때문입니다.  
보통 배열에서는 1바이트로 처리되지만, JVM 내부에서는 4바이트 정렬을 사용할 수도 있습니다.

---

## ✔ 참조 타입(Reference Type)

참조 타입은 **스택에는 주소값(reference)이 저장되고**,  
실제 데이터는 **힙(Heap)**에 저장됩니다.

참조 타입의 종류:

| 종류 | 예시 | 설명 |
|------|------|------|
| **클래스(Class)** | `String`, `Scanner`, 사용자 정의 클래스 | new로 생성되는 모든 객체 |
| **배열(Array)** | `int[] arr = new int[5]` | 배열도 객체이므로 힙에 저장됨 |
| **인터페이스(Interface)** | `List`, `Map` | 구현체의 주소를 참조 |
| **열거형(Enum)** | `enum Role { USER, ADMIN }` | 내부적으로 클래스처럼 동작 |
| **String** | `"hello"` | 리터럴은 String Pool 관리 |

### 참조 타입 저장 구조 예시

```java
String s = new String("Hello");
// Stack: s → 주소값
// Heap: "Hello" 객체 저장

```

## 🧱 Java 변수의 종류 정리 

자바에서 변수는 저장 위치와 생명주기를 기준으로 다음 네 가지로 구분됩니다.

### 1) 지역 변수 (Local Variable)
- 메서드, 생성자, 블록 내부에서 선언  
- **Stack** 저장  
- 반드시 초기화해야 사용 가능  
- 메서드 종료 시 사라짐

### 2) 매개변수 (Parameter)
- 메서드 호출 시 전달되는 입력값  
- **Stack** 저장  
- 지역 변수와 동일한 생명주기

### 3) 인스턴스 변수 (Instance Variable)
- 객체마다 별도로 존재하는 멤버 변수  
- **Heap** 저장  
- 자동 초기화  
- 객체가 GC될 때 소멸

### 4) 클래스 변수 (Static Variable)
- 클래스 로딩 시 한 번만 생성  
- **Method Area** 저장  
- 모든 인스턴스가 공유  
- 프로그램 종료 시 소멸

### ✔ 요약 표

| 종류 | 저장 위치 | 생성 시점 | 소멸 시점 |
|------|-----------|-----------|-----------|
| 지역 변수 | Stack | 메서드 시작 | 메서드 종료 |
| 매개변수 | Stack | 메서드 호출 | 메서드 종료 |
| 인스턴스 변수 | Heap | 객체 생성 | 객체 GC |
| 클래스 변수 | Method Area | 클래스 로딩 | 프로그램 종료 |
