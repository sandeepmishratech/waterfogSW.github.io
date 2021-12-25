---
title: "[Design Pattern]Builder Pattern을 사용하는 이유"
date: 2021-12-25 16:21:13
category: 'DesignPattern'
draft: false
---

정적 팩터리 메서드와 생성자는 선택적 매개변수가 많을때 적절히 대응하기 어렵다는 제약이 있습니다. 이럴때 프로그래머들은 점층적 생성자 패턴을 즐겨 사용하였습니다.

## 1. Telescoping constructor pattern

점층적 생성자 패턴은 n개의 매개변수를 가지는 클래스에 대해 선택 매개변수를 1개 받는 생성자, 2개받는 생성자, ... n개 받는 생성자 의 형태로 생성자를 늘려가는 방식입니다. 

```java
class NutritionFacts {
    private final int servingSize; 	// 필수
    private final int servings; 	// 필수
    private final int calories; 	// 선택
    private final int fat;			// 선택

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
    }
}
```

하지만 이러한 점층적 생성자 패턴은 다음과 같은 단점을 가집니다.

- 매개변수의 개수가 많아질수록 코드를 작성하고, 읽기 어려워집니다. (확장성 및 가독성이 떨어집니다)
- 코드를 읽을때 각 값의 의미가 무엇인지 파악하기 어렵습니다.
- 매개변수의 개수가 몇개인지 주의하여 작성하여야 합니다.
- 매개변수의 순서를 헷갈리게 되면, 런타임에 엉뚱한 동작을 하게 됩니다.

## 2. JavaBeans Pattern

매개변수가 없는 생성자로 객체를 만든 후, 세터 메서드들을 호출하여 원하는 매개변수의 값을 설정하는 방식입니다.

```java
class NutritionFacts {
    private int servingSize = -1; 	// 필수
    private int servings	= -1; 	// 필수
    private int calories	= 0; 	// 선택
    private int fat	 		= 0;	// 선택

    public NutritionFacts() { }

    public void setServingSize(int val);
    public void setServings(int val);
    public void setCalories(int val);
    public void setFat(int val);
}
```

점층적 생성자 패턴에 비해 인스턴스를 더 만들기 쉽고 그결과 더 읽기 쉬운 코드가 되었습니다.

하지만 클라이언트는 객체 하나를 만들기 위해 여러 메서드를 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 됩니다. 때문에 클래스를 불변(final)로 만들수 없게 되고, 스레드 안정성을 얻기위한 추가적업이 필요하게 됩니다.

> 클래스의 모든 필드를 불변으로 만들면 생성 이후에는 읽기 작업만 가능해 지기 때문에 스레드 안정성을 보장합니다.
>
> [Effective Java] Item 17에서는 클래스가 가변적이여야 하는 합당한 이유가 없다면 모든 필드는 private final이어야 한다고 설명합니다.

## 3. Builder Pattern

빌더패턴은 점층적 생성자 패턴의 안정성(Immutable class)과 자바 빈즈 패턴의 가독성(setter method)을 겸비합니다.

클라이언트는 필수 매개변수만으로 생성자를 호출하고 빌더 객체가 제공하는 세터 메서드로 선택 매개변수를 설정합니다. 이후 build 메서드를 호출하여 필요한 객체를 얻습니다.

```java
// 코드 2-3 빌더 패턴 - 점층적 생성자 패턴과 자바빈즈 패턴의 장점만 취했다. (17~18쪽)
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }
        public Builder fat(int val)
        { fat = val;           return this; }
        public Builder sodium(int val)
        { sodium = val;        return this; }
        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }
}
```

`NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8).calories(100).sodium(35).carbohydrate(27).build();`에서 볼 수 있듯, 빌더의세터 메서드들은 빌더 자신(this)를 반환하기 때문에 연쇄적으로 호출이 가능합니다. 이러한 방식을 메서드 호출이 흐르듯 연결된다는 뜻으로 Fluent API 또는 메서드 연쇄(method chaining)라 합니다.

빌더패턴은 점층적 생성자 패턴에 비해 확장에 유리하고 가독성이 높은 동시에 불변성을 확보하여 자바 빈즈 패턴보다 안정적입니다. 따라서 생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는것이 좋습니다.

## Reference

- Effective Java 3th Edition
