---
title: "[Design Pattern]Singleton Pattern"
date: 2021-12-19 16:21:13
category: 'CS'
draft: false
---

## Definition
Singleton의 사전적 의미는 외동, 카드 패에서 한장만 있는것을 지칭하는 말입니다.
이러한 사전적 의미와 비슷하게, Singleton Pattern은 객체를 하나만 생성하도록 제한하기 위해 사용하는 디자인 패턴입니다.

>생성패턴의 종류
>1. 추상 팩토리 패턴(Abstract Factory Pattern)
>2. 빌더 패턴(Builder Pattern)
>3. 팩토리 메서드 패턴(Factory Method Pattern)
>4. 프로토타입 패턴(Prototype Pattern)
>5. 싱글턴 패턴(Singleton pattern)

## Problem

### 1. 싱글톤 패턴은 클래스가 인스턴스를 하나만 가짐을 보장합니다.
이렇게 인스턴스의 수를 통제하는 가장 보편적인 이유는 데이터 베이스나 파일과 같은 일부 공유 리소스에 대한 액세스를 제어하기 위함입니다.

생성자 호출은 항상 새로운 객체를 반환하기 때문에 생성자로는 이러한 동작을 구현할 수 없습니다.

### 2. 인스턴스에 대한 전역 접근을 허용합니다.
전역변수와 마찬가지로 프로그램의 모든 위치에서 인스턴스에 접근할 수 있으며, 다른 코드에 의해 인스턴스가 덮어쓰이지 않도록 보호하기도 합니다.

## Solution

- Singleton 패턴을 적용한 클래스의 생성자의 접근지정자를 private으로 둠으로써 생성자에 의한 새로운 객체 생성을 제한합니다.
- 정적(static) 생성 메서드는 인스턴스가 호출된 적이 없다면, private 생성자를 호출하고, 생성된 인스턴스를 static field에 캐싱하여 이후 호출되더라도 같은 인스턴스를 반환하게 합니다.

### 요구사항
- 개발중인 시스템의 스피커에 접근할 수 있는 클래스를 만들어라

만약 스피커의 인스턴스가 100개 존재할 경우 볼륨을 1올리기 위해서 100개의 모든 스피커 인스턴스에 접근하여
볼륨을 올리는 작업을 수행해 주어야 합니다. 이는 개발의 복잡도를 높이고, 시스템 리소스도 크게 낭비하게 합니다.

다음과 같이 싱글턴 패턴(Singleton Pattern)을 사용하여 이러한 문제를 방지할 수 있습니다.

```java
class SystemSpeaker {
    static private SystemSpeaker speaker;
    private int volume;

    // 외부에서의 생성자 접근을 방지하기 위해 private 접근 지정자를 사용
    private SystemSpeaker() {
        this.volume = 5;
    }

    public static SystemSpeaker getInstance() {
        // speaker 가 null일 경우에만 생성하여 인스턴스가 하나만 생성됨을 보장.
        if(speaker == null) {
            speaker = new SystemSpeaker();
        }
        return speaker;
    }
}
```

### Client Side

```java
public class Singletone {
    public static void main(String[] args) {
        SystemSpeaker speaker1 = SystemSpeaker.getInstance();
        SystemSpeaker speaker2 = SystemSpeaker.getInstance();

        System.out.println(speaker1.equals(speaker2));
    }
}
```

이와같이 프로그램의 클래스가 모든 클라이언트에서 단일 인스턴스만 있어야 하는 경우 Singleton Pattern을 사용합니다.

- Singleton Pattern은 생성자의 접근 지정자를 private으로 설정하여 `getInstance()`함수 이외에 클래스의 개체를 만드는 모든 생성방법을
비활성화 합니다.

## But race condition can occur
하지만 다중 스레드 환경에서 객체의 생성 여부를 확인하고 생성자를 호출하는 과정은 원자성을 보장하지 못하기 때문에 경합 상태(Race Condition)을 발생시킬 수 있습니다.

1. Thread 1 : Speaker 인스턴스가 생성되지 않음을 확인함
2. Thread 1 -> Thread 2 : Context Switch
3. Thread 2 : Speaker 인스턴스가 생성되지 않음을 확인함
4. Thread 2: Speaker Instance생성
5. Thread 2 -> Thread1 : Context Switch
6. Thread 1: Speaker Instance생성(정적 speaker 객체에 대한 경합 상태 발생 -> 2개의 인스턴스 생성)

## Solution

thread-safe한 Singleton Pattern을 위해 다음과 같은 방법들을 고려할 수 있습니다.



### 1. 정적변수 초기화

```java
class SystemSpeaker {
    // 정적 변수는 클래스 로딩시 한번만 수행되기 때문에 인스턴스 초기화는 한번만 수행됩니다.
    static private SystemSpeaker speaker = new SystemSpeaker();
    private int volume;

    private SystemSpeaker() {
        this.volume = 5;
    }

    public static SystemSpeaker getInstance() {
        return speaker;
    }
}
```

이경우 객체 생성을 원치 않더라도 클래스가 로딩되는 시점에 인스턴스가 초기화 되어 메모리를 점유하게 되어 비효율적입니다.

### 2. Synchronized 를 통한 동기화 제어

```java
class SystemSpeaker {
    static private SystemSpeaker speaker;
    private int volume;

    private SystemSpeaker() {
        this.volume = 5;
    }

    // 인스턴스 생성 메서드를 임계구역으로 변경
    public synchronized static SystemSpeaker getInstance() {
        if(speaker == null) {
            speaker = new SystemSpeaker();
        }
        return speaker;
    }
}
```

Synchronize를 통한 객체 초기화는 lock, unlock 과정에서 성능을 크게 저하시킬 수 있습니다.

### 3. Enum
enum 클래스는 클래스처럼 보이게 하는 상수로 서로 관련 있는 상수들을 모아 심볼릭한 명칭의 집합으로 정의한 것입니다.(Jdk 1.5버전 이상에서만 가능한 방법입니다.)
```java
enum SystemSpeaker {
    SPEAKER;

    public static SystemSpeaker getInstance() {
        return SPEAKER;
    }
}
```

Enum을 이용한 방식은 Thread-safe를 보장하지만 컴파일 타임에 enum의 초기화가 진행됩니다.
때문에 Android와 같이 Context에 의존성이 있는 환경에서는 초기화 과정에서 매번 Context정보를 전달해야 하는 문제가 발생합니다.

### 4. Lazy Holder

```java
class SystemSpeaker{
    int volume;

    private SystemSpeaker() {
        this.volume = 5;
    }

    private static class LazyHolder {
        static final SystemSpeaker SPEAKER = new SystemSpeaker();
    }

    public static SystemSpeaker getInstance() {
        return LazyHolder.SPEAKER;
    }
}
```

객체가 필요할때로 초기화를 미룬다는 뜻에서 `Lazy Initialization`이라 부르기도 합니다.
JVM에 의해 `SystemSpeaker`클래스가 로드되면 클래스는 초기화를 거칩니다. 이때 `LazyHolder`클래스는 `getInstance()`메서드가 호출되기 전까지 로드되지 않습니다.

`getInstance()`메서드가 호출되면 `LazyHolder()`클래스를 로드하고 초기화를하는데 클래스의 초기화 단계는 JLS에 의해 순차적, 즉 비동시적임을 보장하므로 thread-safe합니다. 

Singletone Pattern의 thread-safe를 보장하기 위한 가장 좋은 방법입니다.

## Reference

- [https://en.wikipedia.org/wiki/Singleton_pattern](https://en.wikipedia.org/wiki/Singleton_pattern)
- [https://refactoring.guru/design-patterns/singleton](https://refactoring.guru/design-patterns/singleton)
- [https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom](https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom)