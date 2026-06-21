---
layout: post
title: "자바 - 인터페이스, 함수형 인터페이스, 람다"
author: "Mihyun"
header-style: text
tags:
  - TIL
  - java
  - interface
  - lamdba
  - 인터페이스
  - 함수형 인터페이스
  - 람다
---

## 1. 인터페이스란?

- **정의(내 말로)**  
  인터페이스는 “클래스가 반드시 제공해야 할 메서드 목록(역할)을 정의해 둔 설계도”.
- **특징**
  - 메서드의 이름/파라미터/리턴 타입(시그니처)만 정의하고, 실제 구현은 없다. (단, Java 8+부터는 `default`/`static` 메서드, Java 9+부터는 `private` 메서드로 인터페이스 안에 구현을 둘 수도 있다.)
  - 구현은 `implements` 하는 클래스에서 담당한다.
  - 공통된 “역할”만 약속하고, “어떻게 동작하는지”는 각 클래스가 다르게 구현할 수 있게 해준다.

### 예: Animal 인터페이스

```java
public interface Animal {
    void sound();
}
```

구현 클래스들:

```java
public class Dog implements Animal {
    @Override
    public void sound() {
        System.out.println("멍멍");
    }
}

public class Cat implements Animal {
    @Override
    public void sound() {
        System.out.println("야옹");
    }
}
```

사용:

```java
Animal a1 = new Dog();
Animal a2 = new Cat();

a1.sound(); // 멍멍
a2.sound(); // 야옹
```

포인트:
- 변수 타입은 `Animal` 하나로 통일할 수 있다.
- 실제 동작은 `Dog`, `Cat` 구현에 따라 달라진다.
- 인터페이스 덕분에 “역할(Animal)” 기준으로 코드를 작성할 수 있다.

---

## 2. 함수형 인터페이스

- **정의(내 말로)**  
  “추상 메서드가 딱 1개만 있는 인터페이스”.
- 이렇게 메서드가 하나뿐이면, 이 인터페이스는 “함수 하나”처럼 다룰 수 있다.
- 주로 `@FunctionalInterface` 애노테이션을 붙여서 의도/제약을 명시한다.

예:

```java
@FunctionalInterface
public interface MyFunction {
    int apply(int x, int y);
}
```

여기서 `apply(int x, int y)` 메서드 **1개**만 존재 → 함수형 인터페이스.

---

## 3. 구현 클래스 / 익명 클래스 / 람다 비교

같은 함수형 인터페이스를 세 가지 방식으로 구현해보기.

### 3-1. 구현 클래스로 구현

```java
public class AddFunction implements MyFunction {
    @Override
    public int apply(int x, int y) {
        return x + y;
    }
}
```

사용:

```java
MyFunction add = new AddFunction();
int result = add.apply(3, 5); // 8
```

### 3-2. 익명 클래스로 구현

```java
MyFunction add = new MyFunction() {
    @Override
    public int apply(int x, int y) {
        return x + y;
    }
};

int result = add.apply(3, 5);
```

- 일회성 구현을 위해 클래스를 따로 만들지 않고, 즉석에서 구현.

### 3-3. 람다로 구현

```java
MyFunction add = (x, y) -> x + y;

int result = add.apply(3, 5);
```

- `(x, y)` : 파라미터
- `->` : 람다 표시
- `x + y` : 반환할 식(메서드 몸체)

**중요 포인트**  
위 세 가지 방식은 전부 결국 같은 의미:
- 모두 `MyFunction` 인터페이스를 구현하는 방법이다.
- 람다는 “함수형 인터페이스 구현”을 매우 간단한 문법으로 줄여 쓴 것.

---

## 4. 람다를 쓸 수 있는 조건

- **모든 인터페이스에 람다를 쓸 수 있는 건 아니다.**
- “추상 메서드가 딱 1개인 인터페이스(= 함수형 인터페이스)”에만 람다를 쓸 수 있다.
- 보통 `@FunctionalInterface`가 붙어 있다.
  - 예: `Runnable`, `Comparator<T>`, `java.util.function.*` 계열 등

---

## 5. 스프링/실무에서의 예 – 이벤트 리스너

스프링에서 자주 보게 될 인터페이스: `ApplicationListener<E>`

(단순화된 형태)

```java
public interface ApplicationListener<E extends ApplicationEvent> {
    void onApplicationEvent(E event);
}
```

- 추상 메서드 `onApplicationEvent` 하나 → 함수형 인터페이스처럼 쓸 수 있다.

### 5-1. 익명 클래스로 구현

```java
ApplicationListener<MyEvent> listener = new ApplicationListener<MyEvent>() {
    @Override
    public void onApplicationEvent(MyEvent event) {
        System.out.println("이벤트 발생: " + event);
    }
};
```

### 5-2. 람다로 구현

```java
ApplicationListener<MyEvent> listener =
        event -> System.out.println("이벤트 발생: " + event);
```

여기서 책의 문장:

> “이들은 함수이므로 람다로 구현할 수 있다”

의 의미:
- `ApplicationListener` 같은 인터페이스들은 추상 메서드 1개를 가지는 **함수형 인터페이스**이다.
- 따라서, 그 인터페이스 타입의 객체를 만들 때 **람다로 구현할 수 있다**는 뜻.

---

## 6. 오늘 헷갈렸던 포인트 정리

- 처음에는 “이들은 함수이므로 람다로 구현할 수 있다”라는 말이 이해가 안 됐다.
- 실제 의미:
  - “이 인터페이스들은 함수형 인터페이스라서,  
    굳이 익명 클래스 안 쓰고, 람다 문법으로 바로 구현해도 된다”는 말.
- 핵심 개념:
  1. 인터페이스 = 역할(메서드 목록) 설계도
  2. 함수형 인터페이스 = 추상 메서드 1개
  3. 함수형 인터페이스는 익명 클래스 대신 람다로 구현 가능

이 세 가지를 연결해서 이해하면,  
스프링 책에서 인터페이스/리스너/콜백 관련 설명을 볼 때 훨씬 덜 헷갈릴 것 같다.
