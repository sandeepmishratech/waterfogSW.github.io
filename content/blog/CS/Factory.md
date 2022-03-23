---
title: "[Design Pattern]Factory Pattern"
date: 2022-03-23 16:21:13
category: 'CS'
draft: false
---

## Factory Pattern

- 생성패턴의 한 종류
- 객체를 생성하는 공장(Factory)


![image](https://user-images.githubusercontent.com/28651727/159606036-eaafdc35-759b-468d-b38d-e2a4b84cf610.png)

```java
interface Animal {
    void speak();
}

class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("meow!");
    }
}

class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("bark!");
    }
}
```

- 위와 같이 Animal을 상속받는 Dog, Cat클래스가 존재한다고 가정합니다.


```java
class AnimalFactory {
    Animal createAnimal(String type) {
        if (type.equals("Dog")) {
            return new Dog();
        } else if (type.equals("Cat")) {
            return new Cat();
        } else {
            return null;
        }
    }
}

public class Main {
    static final AnimalFactory af = new AnimalFactory();
    
    public static void main(String[] args) {
        Animal dog = af.createAnimal("Dog");
        Animal cat = af.createAnimal("Cat");

        dog.speak(); // bark
        cat.speak(); // meow
    }
}
```

- `createAnimal()`함수는 인자로 받은 type에 따라 Cat을 생성해서 반환하거나, Dog를 생성해서 반환해 줍니다.

**팩토리 패턴의 장점?**

- 어떠한 상황에서는 객체 생성과정이 복잡할 수 있는데, 이러한 팩토리 패턴을 통해 복잡한 객체의 생성과정을 클라이언트가 직접 다룰 필요가 없어집니다.


## Factroy Method Pattern

![image](https://user-images.githubusercontent.com/28651727/159606063-7023f91a-7ffc-493c-8a27-5d8c821547b8.png)


```java
interface AnimalFactory {
    Animal createAnimal();
}

class DogFactory implements AnimalFactory {
    @Override
    public Animal createAnimal() {
        return new Dog();
    }
}

class CatFactory implements AnimalFactory {
    @Override
    public Animal createAnimal() {
        return new Cat();
    }
}

public class Main {
    static final AnimalFactory df = new DogFactory();
    static final AnimalFactory cf = new CatFactory();

    public static void main(String[] args) {
        Animal dog = df.createAnimal();
        Animal cat = cf.createAnimal();

        dog.speak();
        cat.speak();
    }
}
```

- 꼭 생성할 클래스가 팩토리 클래스와 1 : 1 매칭이 될 필요는 없습니다.
- 객체를 생성하는 메서드가 상속이 되었느냐가 Factroy Method Pattern의 핵심입니다.

## Diff (Factory Pattern vs Factory Method Pattern)

Factory패턴은 Factory Method 패턴보다 단순한 형태로 인스턴스화 로직을 클라이언트에게 드러내지 않고, 직접 새로운 객체를 생성하여 제공합니다. 

Factory Method Pattern은 객체를 생성하는 인터페이스로, 하위 구상클래스가 객체를 생성하여 제공합니다.