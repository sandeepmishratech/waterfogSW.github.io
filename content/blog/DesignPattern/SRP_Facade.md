---
title: "[Design Pattern]Facade 패턴을 통한 SRP원칙 준수"
date: 2021-12-29 16:21:13
category: 'DesignPattern'
draft: false
---


## Goal

- Facade 패턴에대한 이해
- SRP원칙에 대한 이해

## SRP : 단일 책임 원칙(Single responsibility principle)

객체는 변화해야 할 단 한가지 이유만을 가져야 합니다.

쉽게 생각하면 변경이 있을때 파급 효과가 적다면, 즉 수정해야 할 코드가 적다면 단일 책임 원칙을 잘 따른것이라 볼 수 있습니다.

다음과 같이 SRP위반 사례를 예시로 SRP를 지켜야 하는 이유에 대해 알아보겠습니다.

## Problem 1 : Accidental duplication

다음은 급여 애플리케이션의 Employee 클래스입니다.

Employ 클래스에는 `calculatePay()`, `reportHours()`, `save()` 메서드가 존재하는데 각각은 회계팀, 인사팀, 전산팀이 주로 사용합니다.

개발자가 이 세 메서드를 Employee 단일 클래스에 배치하여 서로 결합되었습니다. 이로인해 각 엑터의 결정이 다른 엑터가 의존하는 부분에 영향을 줄 수 있게 됩니다.

```java
class Employee {
    int hour;

    // Actor : 회계팀
    public int calculatePay() { 
        ...
        regularHour();
        ... 
    }
    
    // Actor : 인사팀
    public void reportHours() { 
        ...
        regularHour();
        ... 
    }
    
    // Actor : 전산팀
    public void save() { ... }

    // 초과 업무 시간을 제외한 업무시간 계산
    private int regularHour() { ... }
}
```

`calculatePay()`와 `reportHours()`는 초과 업무 시간을 제외한 업무시간을 계산하는 `regularHour()`를 공유합니다. 회계팀에서 초과 업무 시간을 제외한 업무시간 계산 방식을 변경하고자 `regularHour()`를 수정하게 되면 기존의 방식을 그대로 사용하던 인사팀은 이에 영향을 받게 되고, `reportHours()`는 기존의 방식대로 동작하지 못하게 됩니다.

이러한 문제는 서로 다른 액터가 의존하는 코드를 너무가까이(단일 클래스)에 배치했기 때문에 발생합니다.

## Problem 2 : Merge

인사팀에서는 `reportHours()`를 전산팀에서는 `Employee`의 스키마를 수정하기로 동시에 결정합니다. 각 팀의 수정사항이 반영될 때 충돌 즉, 병합이 발생하게 됩니다.

## Solve

이러한 문제를 해결 하기 위해서는 데이터를 가지고 있는 `EmployeeData`클래스와 각 메서드를 가지고 있는 클래스로 분리하고 메서드를 가진 각 클래스들이 `EmployeeData`클래스를 공유하도록 하면 됩니다. 하지만 이경우 개발자가 메서드를 가진 세 클래스를 인스턴스화 하고 추적해야한다는게 단점입니다.

이경우 퍼사드 패턴(Facade Pattern)을 적용하여 클래스의 생성하여 인스턴스의 생성을 위임할 수 있습니다. 퍼사드 패턴은 서브 클래스간의 통합 인터페이스를 제공하는 역할로 클라이언트는 퍼사드 객체에서 제공하는 메서드를 호출함으로써 복잡한 서브클래스 사용을 간편하게 할 수 있습니다.

```java
class EmployeeData {
    //data(공유자원)
    int hour;

    private EmployeeData() {
        this.hour = 0;
    }

    private static class LazyHolder {
        static final EmployeeData EMPLOYEE_DATA = new EmployeeData();
    }

    public static EmployeeData getInstance() {
        return LazyHolder.EMPLOYEE_DATA;
    }
}

class PayCalculator {
    final EmployeeData data = EmployeeData.getInstance();

    void calculatePay() {
        // 예제 코드
        data.hour += 1;
    }
}

class HourReporter {
    final EmployeeData data = EmployeeData.getInstance();

    void reportHours() {
        // 예제 코드
        data.hour += 1;
    }
}

class EmployeeSaver {
    final EmployeeData data = EmployeeData.getInstance();

    void save() {
        // 예제 코드
        data.hour += 1;
        System.out.println(data.hour);
    }
}

class EmployeeFacade {
    private final PayCalculator payCalculator;
    private final HourReporter hourReporter;
    private final EmployeeSaver employeeSaver;

    public EmployeeFacade() {
        payCalculator = new PayCalculator();
        hourReporter = new HourReporter();
        employeeSaver = new EmployeeSaver();
    }

    public void calculatePay() {
        payCalculator.calculatePay();
    }

    public void reportHour() {
        hourReporter.reportHours();
    }

    public void save() {
        employeeSaver.save();
    }
}

public class Facade {
    public static void main(String[] args) {
        EmployeeFacade employeeFacade = new EmployeeFacade();

        employeeFacade.calculatePay();
        employeeFacade.reportHour();
        employeeFacade.save();
    }
}
```

Singleton pattern을 적용하여 공유자원인 `EmployeeData`에 대한 thread-safety를 확보하였고, `EmployeeFacade` 클래스를 통해 객체의 생성을 위임하여 개발자가 메서드를 가진 세 클래스에 대한 인스턴스화를 하지 않아도 되도록 설계를 하였습니다.

다만 이러한 Facade 패턴을 관련 없는 메소드를 엮기 위해 사용할 경우 [God-object](https://en.wikipedia.org/wiki/God_object)가 될 수 있으므로 성능향상(관련없는 객체도)과 유지보수성(관련있는 객체만)의 Trade-off를 잘 고려하여 사용하여야 합니다.

## Reference

- [https://refactoring.guru/design-patterns/facade](https://refactoring.guru/design-patterns/facade)
- Clean Architecture