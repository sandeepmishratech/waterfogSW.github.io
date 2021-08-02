---
layout: article
title: "[Java] 객체지향 프로그래밍 - 2"
excerpt: 객체지향 프로그래밍 - 2
categories:
  - 'Java'
tags:
  - 'Java'
  - 'OOP'
last_modified_at: 2021-08-01T08:06:00-05:00
mode: immersive
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
toc: true
comment: true
cover: https://user-images.githubusercontent.com/28651727/127760398-a59ebbf6-f35a-4bec-a3ee-c47061a31f42.png

---

# [JAVA] 객체지향 프로그래밍 2

## 1. 상속

### 1.1 상속의 정의
- 조상 클래스 : 부모(parent), 상위(super), 기반(base) 클래스
- 자손 클래스 : 자식(child), 하위(sub), 파생(derived) 클래스

자바에서 상속은 다음과 같이 키워드 `extends`를 통해 구현한다.
```java
class Parent {
    int age;
    ...
}
class Child extends Parent { 
    void play();
}
```

자손 클래스는 조상 클래스의 모든 멤버를 상속받기 때문에 Child클래스는 Parent클래스의 멤버들을 포함한다.

따라서 위의 소스코드에서 Child 클래스는 `age`멤버 변수를 Parent클래스로부터 상속받아 추가된 것과 같은 효과를 얻는다.

반면 자손 클래스의 변경은 조상 클래스에 아무런 영향을 주지 못한다.

Child 클래스의 `play()`함수는 Parent 클래스에서는 사용할 수 없다.

자손 클래스의 인스턴스를 생성하면 조상 클래스의 멤버와 자손 클래스의 멤버가 합쳐진 하나의 인스턴스로 생성된다.

### 1.2 클래스간의 관계

원을 표현하기 위해 Circle이라는 클래스를 다음과 같이 작성하였다 가정하자.

```java
class Circle {
    int x;
    int y;
    int r;
}
```

**포함관계** 로 표현
```java 
class Point {
    int x;
    int y;
}

class Circle {
    Point c = new Point();
    int r;
}
```

**상속관계**로 표현
```java
class Circle extends Point {
    int r;
}
```

이렇게 Point클래스를 상속관계로 두느냐, 포함관계로 두느냐는 큰 차이가 있어보이지는 않는다.

클래스간의 관계를 결정하기 위해서는 다음과 같은 기준을 활용하면 보다 명확해진다.

- 상속관계 : ~은 ~이다(원은 점이다)
- 포함관계 : ~은 ~을 가지고 있다(원은 점을 가지고 있다)

`원은 점을 가지고 있다`가 조금더 매끄러운 문장이다. 따라서 Circle클래스와 Point클래스는 포함관계를 맺어주는것이 더 좋다.

### 1.3 단일 상속(single inheritance)

C++에서는 다중상속을 허용하지만 자바에서는 단일 상속만을 허용한다.  
단일상속은 하나의 조상 클래스만을 가질 수 있기 때문에 클래스 간의 관계가 보다 명확해지고 코드를 더 신뢰할수 ㅣㅇㅆ게 만들어준다.

### 1.4 Object클래스

Object클래스는 모든 클래스 상속계층의 최상위 조상클래스이다. `class Tv{}`라는 클래스를 생성하면 컴파일러는 자동적으로 `extends Object`를 추가하여 Tv클래스가 Object클래스로부터 상속받도록 한다.
> 이미 클래스가 다른 클래스를 상속한다면 단일 상속 규칙을 위해 Object클래스를 상속하지 않는다.

## 2. 오버라이딩(Overriding)

```java
class Point {
    int x;
    int y;

    String getLocation() {
        return "x :" + x + ", y :" + y;
    }
}

class Point3D extends Point{
    int z;
    String getLocation() {
        return "x :" + x + ", y :" + y + ", z :" + z;
    }
}
```

### 2.1 오버라이딩 조건
- 이름이 같아야 한다.
- 매개변수가 같아야 한다
- 반환타입이 같아야 한다

1. 접근제어자는 조상클래스의 메서드보다 좁은 범위로 변경할 수 없다.
   - protected <= public
2. 조상클래스의 메서드보다 많은 수의 예외를 선언할 수 없다.
   - 아래의 경우 부모클래스가 더 많은 예외를 가지고 있는것 처럼 보이지만 `Exception`은 모든 예외의 최고 조상이므로 가장 많은 개수의 예외를 던질 수 있도록 선언한 것이다. 따라서 잘못된 오버라이딩이다.
```java
class Parent {
    void pMethod() throws IOException, SQLException {
        ...
    }

    void cMethod() throws Excetpion {
        ...
    }
}
``` 

3. 인스턴스 메서드를 static메서드 도는 그 반대로 변경할 수 없다.
   - static메서드 호출시에는 `참조변수.메서드()` 보다는 `클래스이름.메서드()`의 방식으로 호출하는 것이 바람직하다.

### 2.2 오버로딩 vs 오버라이딩

**오버로딩** - 기존에 없던 새로운 메서드 정의
**오버라이딩** - 상속받은 메서드의 내용을 변경하는것

```java
class Parent {
    void parentMethod() {
        ...
    }
}

class Child {
    void parentMethod() {}      // 오버라이딩
    void parentMethod(int i) {} // 오버로딩
}
```

### 2.3 super

super는 조상클래스로부터 상속받은 멤버를 참조하는데 사용되는 참조변수이다.

```java
class Parent {
    int x = 10;
    void print() {
        System.out.println(x);
    }
}

class Child extends Parent {
    int x = 20;
    
    void method() {
        System.out.println(x);          // 20
        System.out.println(this.x);     // 20
        System.out.println(super.x);    // 10
    }

    void print_child() {
        super.print();                  // 10
    }
}

```

this()와 마찬가지로 super()또한 생성자이지만, 조상 클래스의 생성자를 호출하는데 사용된다.

```java
class Point {
    int x;
    int y;
    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Point3D {
    int x;
    int y;
    int z;
    
    Point3D(int x, int y, int z) {
        super(x, y);
        int this.z = z;
        
    }
}
```

## 3. Package & import

### 3.1 Package

- 하나의 소스파일은 첫 번째 문장으로 단 한 번의 패키지 선언만을 허용
- 모든 클래스는 반드시 하나의 패키지에 속해야한다.
- 패키지는 점(.)을 구분자로 계층구조로 구성할 수 있다.
- 패키지는 물리적으로 클래스 파일을 포함하는 하나의 디렉토리이다.

### 3.2 import

일반적으로 소스파일은
>1. package
>2. import
>3. 클래스 선언  

순으로이루어 진다.

한 패키지에서 여러 클래스를 사용하는 경우 클래스의 이름을 일일이 지정해주는 것보다 `패키지명.*`으로 하는것이 편리하다

`import java.util.*`

### 3.3 static import

import문을 사용하면 클래스의 패키지 명을 생략할 수 있는 것과 같이 static import문을 사용하면 static멤버를 호출할 때 클래스 이름을 생략할 수 있다.

```java
System.out.println(Math.random());
```

```java
import static java.lang.Math.random;
import static java.lang.System.out;

out.println(random());
```

## 4. 제어자

**접근 제어자**
- public, protected, default, private

**그 외**
- static, final, abstract, native, transient, synchronized, volatile, strictfp

### 4.1 static

static은 `클래스의`, `공통적인`이라는 의미를 갖고 잇다.

static 멤버변수(클래스변수)는 인스턴스를 생성하지 않고도 사용할 수 있으며, 모든인스턴스가 값을 공유한다.

### 4.2 final

c++의 const와 같이 변수에 사용하게되면 상수가 되며, 메서드에 사용되면 오버라이딩 할 수없게 되며, 클래스에 사용하면 자손클래스를 정의하지 못하게 된다.

```java
final class FinalTest {
    final int MAX_SIZE = 10;

    final void getMaxSze() {
        final int LV = MAX_SIZE;
        return MAX_SIZE;
    }
}
```

### 4.3 abstract

클래스에서의 abstract는 클래스 내에 추상 메서드가 선언되어 있음을 의미하며, 메서드에서 abstract는 선언부만 작성하고 구현부는 작성하지 않은 추상 메서드임을 알린다

```java
abstract class AbstractTest {
    abstract void move();
}
```

추상클래스는 아직 완성되지 않은 미완성 설계도이므로 인스턴스를 생성할 수 없다.

### 4.4 접근제어자

- private
  - 같은 클래스 내에서만 접근 가능
- default
  - 같은 패키지 내에서만 접근 가능
- protected
  - 같은 패키지, 다른 패키지의 자손클래스에서 접근 가능
- public
  - 접근 제한이 없다

이러한 접근 제어자는 클래스 내부의 데이터를 **캡슐화** 하여 보호하기 위해서 사용한다.

#### 생성자의 접근 제어자
생성자에 접근 제어자를 사용하면 인스턴스 생성을 제한하여 클래스 내부에서만 인스턴스를 생성할 수 있다.

```java
class Singleton {
    private Sigleton() {
        ...
    }
    private static instanceNum = 0;
    private static Singleton s = new singleton();
    // static 으로 선언하여 인스턴스를 미리생성, getInstance()에서 사용할 수 있도록 한다
    public static Singleton getInstance() {
        if (instanceNum > 5) return -1;
        else {
            instanceNum++;
            return s;
        }
    }
}
```
이와 같이 public 메서드를 통해 인턴스에 접근하게 하여 사용할 수 있는 인스턴스의 개수를 제한할 수 있다.

### 4.5 제어자 조합

| 대상     | 사용가능한 제어자                        |
| -------- | ---------------------------------------- |
| 클래스   | public, default, final, abstract         |
| 메서드   | 모든 접근제어자, final, abstract, static |
| 멤버변수 | final, static                            |
| 지역변수 | static                                   |

> 1. 메서드에 static과 abstract를 함께 사용할 수 없다.
> 2. 클래스에 abstract와 final을 동시에 사용할 수 없다.
> 3. abstract메서드의 접근 제어자가 private일 수 없다.
> 4. 메서드에 private과 final을 같이 사용할 필요는 없다.


## 5. 다형성

다형성이란 여러가지 형태를 가질 수 있는 능력을 의미하며, 자바에서는 한 타입(조상클래스)의 참조변수로 여러 타입(자손클래스)의 객체를 참조할 수 있도록 함으로써 다형성을 프로그램적으로 구현하였다.

주의할 점은 조상타입의 참조변수로 자손타입의 인스턴스를 참조할 수는 있지만, 자손타입의 참조변수로 조상 타입의 인스턴스를 참조하는것은 존재하지 않는 멤버변수를 참조할 가능성이 있으므로 허용하지 않는다.

> 자손타입 -> 조상타입 : 형변환 생략가능
> 조상타입 -> 자손타입 : 형변환 생략불가능

### 5.1 instanceof연산자

상속관계에 있는 타입간 형변환은 자유롭게 수행될 수있지만, 참조변수가 가리키는 인스턴스의 자손타입으로의 형변환은 허용되지 않는다.
따라서 참조변수가 가리키는 인스턴스의 타입이 무엇인지 확인하는 것이 중요하다.

참조변수가 참조하고 있는 인스턴스의 실제 타입을 알기 위해 instanceof연산자를 사용할 수 있다.

```java
void doWork(Car c) {
    if (c instanceof FireEngine) {
        FireEngine fe = (FireEngine)c;
        ...
    } else if (c instanceof Ambulance) {
        Ambulance ac = (Ambulance)c;
        ...
    }
}
```

어떤 타입에 대한 instanceof연산의 결과가 true라는것은 검사한 타입으로의 형변환이 가능하다는 것이다.

`c instanceof FireEngine == true`
- c는 FireEngine클래스 타입으로 형변환이 가능하다.

### 5.2 참조변수와 인스턴스의 연결

메서드의 경우 조상클래스의 메서드를 자손의 클래스에서 오버라이딩한 경우에도 참조변수의 타입에 관계없이 항상 실제 인스턴스의 메서드가 호출되지만, 멤버변수의 경우 참조변수의 타입에 따라 달라진다.

```java
class BindingTest {
    public static void main(String[] args) {
        Parent p = new Child();
        Child c = new Child();

        System.out.println(p.x); // 100
        System.out.println(c.x); // 200

        p.method(); // Child Method
        c.method(); // Child Method

    }
}

class Parent {
    int x = 100;

    void method() {
        System.out.println("Parent Method");
    }
}

class Child extends Parent {
    int x = 200;

    void method() {
        System.out.println("Child Method");
    }
}
```

## 6. 추상클래스

추상클래스는 클래스로서의 역할은 못하지만 새로운 클래스를 작성하는데 바탕이 되는 조상클래스로서 중요한 의미를 갖는다.

추상메서드는 메서드의 내용이 상속받는 클래스에 따라 달라질 수 있기에 조상클래스에서는 선언부만을 작성하고, 주석을 덧붙여 어떤 기능을 수행할 목적으로 작성되었는지 알려 주고, 실제 내용은 상속받는 클래스에서 구현하도록 비워두는 것이다. 

```java
/* 주석을 통해 메서드의 목적 설명*/
abstract 리턴타입 메서드이름(); // 구현부가 없으므로 {} -> ;
```

여러 클래스에 공통적으로 사용될 수 있는 클래스를 바로 작성하기도 하고, 기존 클래스의 공통적인 부분을 뽑아 추상클래스로 만든후 상속하도록 하는 경우도 있다.

## 7. 인터페이스

인터페이스는 일종의 추상클래스이나, 일반 메서드 또는 멤버변수를 구성원으로 가질 수 없다.  
오직 추상 메서드와 상수만을 멤버로 가질 수 있으며, 어떠한 다른 요소도 허용하지 않는다.

```java
interface Name {
    public static final 타입 상수이름 = 값;
    public abstract 메서드이름(매개변수 목록);
}
```

- 모든 멤버변수는 pbulic static final이어야 하며, 생략할 수 있다.
- 모든 메서드는 public abstract이어야 하며 생략할 수 있다.

### 7.1 인터페이스 상속

인터페이스는 인터페이스로부터만 상속받을 수 있으며 클래스와 달리 다중상속이 가능하다

```java
interface Movable {
    void move(int x, int y);
}

interface Attackable {
    void attack(Unit u);
}

interface Fightable extends Movable, Attackable {}
```

### 7.2 인터페이스 구현

인터페이스는 상속을 통해 구현하지만, 확장한다는 의미에서 `extends`를 사용하는 일반 클래스와 달리 구현한다는 의미의 `implements`를 사용한다.

```java
class 클래스이름 implements 인터페이스 이름 {
    /*추상 메서드 구현*/
    ...
}

class Fighter implements Fightable {
    public void move(int x, int y) {
        ...
    }
    public void attack(Unit u) {
        ...
    }
}
```

### 7.3 인터페이스에서의 다중상속?

인터페이스는 stataic상수만 정의할 수 있으므로 조상클래스의 멤버변수와 충돌하는 경우는 거의 없으며, 충돌한다 하더라도 클래스의 이름을 붙여 구분이 가능하다.

### 7.4 인터페이스의 장점

1. 개발시간 단축
   - 인터페이스를 구현하는 클래스, 메소드 호출하여 사용하는 쪽을 동시에 개발을 진행할 수 있다.
2. 표준화 가능
   - 기본틀을 인터페이스로 작성한 후, 개발자들에게 인터페이스를 구현하여 프로그램을 작성하도록 함으로써 보다 일관되고 정형화 된 프로그램의 개발이 가능하다.
3. 관계없는 클래스들에게 관계를 맺어 줄 수 있다
   - 서로 관계없는 클래스들에게 하나의 인터페이스를 공톡적으로 구현하도록 하여 관계를 맺어줄 수 있다.
4. 독립적인 프로그래밍이 가능하다
   - 클래스의 선언과 구현을 분리시켜 구현에 독립적인 프로그램을 작성할 수 있다.

## 8. 내부 클래스

### 8.1 내부클래스

내부클래스는 클래스 내에 선언된 클래스로 두 클래스가 긴밀한 관계에 있을때 사용한다.

**장점**
- 내부 클래스에서 외부 클래스의 멤버들을 쉽게 접근할 수 있다.
- 코드의 복잡성을 줄일 수 있다.

```java
class Outer {
    private int outerIv = 0;
    static int outerCv = 0;
    // instance 클래스
    private class InstanceIneer {
        final static int CONST = 100; // final static은 상수이므로 허용
    }
    
    // static 클래스
    protected static class StaticInner{
        static int cv = 200; // static class만 static 변수를 선언할 수 있다.
    }

    void method() {
        int lv         = outerLv;  // 외부클래스의 지역변수
        final int LV   = 0;
        // local 클래스
        class LocalInner {
            int liv1 = outerIv;
            int liv2 = outerCv;
            int liv3 = lv;  // 에러가 발생하거나 JDK 1.8이상일 경우 lv는 final lv가 된다.
            int liv4 = LV;
        }
    }
}
```

### 8.2 익명클래스

익명클래스는 다른 내부클래스와는 달리 선언과 객체 생성을 동시에 하기 때문에 이름이 없고, 단한번 객체를 생성하는데 사용할 수 있는 일회용 클래스이다.

```java
class Inner {
    Object iv = new object() {
        void method() {...}
    };
}
```

익명클래스는 이름이 없기 때문에 `외부클래스명$숫자.class`의 형식으로 클래스파일 명이 결정된다.