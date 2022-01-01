---
title: "[Java] 추상클래스와 인터페이스"
date: 2022-01-01 16:21:13
category: 'Java'
draft: false
---
# [Java] 추상클래스와 인터페이스

### Goal
- 추상클래스와 인터페이스에 대한 이해

## 추상클래스(Abstract Class)

추상클래스는 구현부가 없어 인스턴스를 생성할 수 없고, 상속을 통한 자손클래스로만 구현이 가능합니다. 이러한 추상클래스의 존재이유는 객체지향 설계의 추상 개념과 맞닿아 있습니다. 

추상 개념은 구체적인 사물들의 공통적인 특징을 파악해서 이를 하나의 개념으로 나타내는것을 말합니다. 예를들어 구, 사면체, 육면체 등과 같은 도형은 입체라는 공통적인 특징을 통해 입체도형이라는 개념으로 나타낼 수 있습니다. 하지만, 이러한 도형의 겉넓이, 부피를 구하는 구체적인 행동을 구현하고자 한다면, 도형의 형태에 따라 겉넓이와 부피를 구하는 방식이 다르기 때문에 입체도형의 입장에서는 이러한 행동을 구현하기 어렵습니다. 

이렇게 각각의 도형의 상위 클래스의 입장에서 하위클래스의 구체적인 행동내용을 정의 할 수 없기 때문에 추상클래스를 사용합니다.

```java
abstract class Figure {
    abstract int getSurfaceArea();  // 추상 메서드
    abstract int getVolume();       // 추상 메서드
}

class Ball extends Figure {
    int getSurfaceArea() { ... };   // 추상 메서드 구현
    int getVolume() { ... };        // 추상 메서드 구현
}

class Tetrahedron extends Figure {
    int getSurfaceArea() { ... };   // 추상 메서드 구현
    int getVolume() { ... };        // 추상 메서드 구현

}

```

추상화는 구체화와 반대되는 의미로 이해할 수 있습니다. 상속 계층도에서 상위에 위치할 수록 추상화 정도가 심해져 공통요소만 남게 되며, 하위에 위치할 수록 구체화 정도가 심해져 세분화 된다고 할 수 있습니다.

## 인터페이스(Interface)

추상클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 합니다. 자바는 단일 상속만을 지원하기 때문에 추상클래스를 통한 설계 방식은 새로운 타입을 정의하는데 큰 제약을 받게 됩니다.

```java
abstract class TV {
    void show();
}

abstract class VCR {
    void scanTape();
}

class TVCR extends TV,VCR { // 이와 같은 다중 상속은 허용하지 않습니다.
    ...
}
```

반면 인터페이스의 경우 선언한 추상메서드를 모두 정의하고 일반 규약만 잘 지킨다면 어떤 클래스를 상속했든 같은 타입으로 취급되어 **다중 상속**이 가능합니다.

```java
interface TV {
    void show();
}

interface VCR {
    void scanTape();
}

interface TVCR extends TV, VCR {
    
}
```

이러한 다중 상속의 특성 때문에 인터페이스로는 계층구조가 없는 타입 프레임 워크를 만들 수 있습니다. 

다음 코드에서 가수(Singer)와 작곡가(Songwriter)는 어느 쪽이 상위 개념이라 명확하게 구분하기 어렵습니다. 이때 작곡도 하는 가수를 구현하고자 하는 경우 가수에 작곡가의 코드를 구현해도 문제되지 않지만, 다중 상속을 통해 제 3의 인터페이스를 정의할 수도 있습니다.

```java
public interface Singer {
    AudioClip sing(Song s);
}

public interface Songwriter {
    Song compose(int chartPosition);
}

public interface SingerSongwriter extends Singer, Songwriter {
    AudioClip strum();
    void actSensivitve();
}

```

인터페이스는 추상클래스보다 추상화 정도가 높아 일반 메서드나 멤버변수를 구성원으로 가질 수 없습니다. 오직 추상 메서드와 상수만을 허용합니다.

- 인터페이스 작성시 모든 멤버변수는 `public static final`이어야 하며, 이를 생략할 수 있다.
- 모든 메서드는 `public abstract`이어야 하며, 이를 생략할 수 있다.

추상클래스가 상속을 통해 추상 메서드를 완성하는 것 처럼, 인터페이스도 추상메서드를 구현하는 클래스를 작성해야 합니다. 다만 클래스는 확장한다는 의미의 키워드 `extends`를 사용하지만, 인터페이스는 구현한다는 의미의 `implements`를 사용합니다.

```java

interface TV {
    void show();
}

interface VCR {
    void scanTape();
}

interface TVCR extends TV, VCR {

}

class LGTV implements TVCR {
    public void show() { ... }
    public void scanTape() { ... }
}
```

## Reference
- Java의 정석
- Effective Java