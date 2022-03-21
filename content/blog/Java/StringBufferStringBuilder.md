---
title: "[Java] StringBuilder vs StringBuffer"
date: 2022-03-21 16:21:13
category: 'Java'
draft: false
---

### Goal
- StringBuilder와 StringBuffer의 차이

### StringBuilder, StringBuffer
- StringBuilder, StringBuffer 둘다 mutable한 객체이며 데이터 변경이 빈번한 경우에 성능면에서 유리합니다. 하지만 둘사이에는 약간의 차이가 존재합니다.

### StringBuffer
- StringBuffer는 데이터 변경시 스레드간 동기화를 진행하기 때문에 때문에 thread-safe합니다.

  - ```java
        @Override
        @HotSpotIntrinsicCandidate
        public synchronized StringBuffer append(String str) {
            toStringCache = null;
            super.append(str);
            return this;
        }
    ```


> **Synchronized Method**
>
> 인스턴스 단위로 lock을 설정하며, 메서드가 시작될 때 부터 종료될 때 까지 동기화가 발생합니다. 동일 인스턴스 내의 다른 synchronized method에 대해서도 동일한 lock을 설정합니다.

### StringBuilder

- 반면 StringBuilder는 스레드간 동기화를 지원하지 않기 때문에 thread-safe하지 않으나, 단일 스레드에서의 성능은 StringBuffer보다 우수합니다.

  - ```java
        @Override
        @HotSpotIntrinsicCandidate
        public StringBuilder append(String str) {
            super.append(str);
            return this;
        }
    ```
    
  - 이러한 차이때문에 StringBuilder의 주석에서는 다음과 같이 멀티 스레드 환경에서는 StringBuilder를 사용할것을 권장하고 있습니다.
  
  - ```
      * Instances of {@code StringBuilder} are not safe for
      * use by multiple threads. If such synchronization is required then it is
      * recommended that {@link java.lang.StringBuffer} be used.
    ```