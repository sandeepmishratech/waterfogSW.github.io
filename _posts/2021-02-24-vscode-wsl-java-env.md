---
title: VScode + WSL에 JAVA개발환경 구성하기
excerpt: VScode + WSL에 JAVA개발환경 구성하기
categories:
  - 'Env'
tags:
  - 'Java'
  - 'WSL'
  - 'VScode'
last_modified_at: 2021-02-24T08:06:00-05:00
---

알고리즘 강의를 수강하게 되면서 JAVA를 거의 처음 접하게 되었습니다.
JAVA를 위한 IntelliJ, Eclipse와 같은 IDE도 있지만 
기존에 사용하던 VScode과 WSL을 그대로 사용하고 싶어서 간단하게 해당 환경에 
JAVA개발환경을 구축하는 과정을 기록하고자 합니다.

VScode에서 Remote-WSL을 통해 WSL이 연결되어있다는 가정하에 시작합니다.

### JDK, JRE, Maven설치

우선 터미널에 다음 코드를 입력하여 JRE와 JDK, maven을 설치합니다
```
sudo apt install openjdk-11-jre-headless openjdk-11-jdk-headless maven
```
- jdk : 자바 개발 키트
- jre : 자바 개발 환경 
- maven : java프로젝트 관리도구  

여기서 사용하고 싶은 버전이 따로 있다면 8대신 다른 숫자를 집어 넣으면 됩니다.  
headless는 GUI가 없는 버전으로 따로 그래픽 인터페이스를 사용할 일이 없으므로 headless로 설치합니다

설치가 완료되면 다음코드를 입력하여 설치가 정상적으로 되었는지 확인합니다.
```
java -version
```

### VScode JAVA Extensions설치
설치가 정상적으로 되었다면 VScode에서 Extensions를 설치해 줄 차례입니다.
VScode의 마켓플레이스에 Java Extension Pack을 검색하여 설치합니다. 

설치후 test.java파일을 생성하여 컴파일 해봅니다.
```java
package java_test;

public class test {

    public static void main(String[] args) {
        System.out.println("hello world");
    }
}
```

C/C++ 와 같은 다른 컴파일러를 VScode에서 사용하고 있다면 VScode의 왼쪽 메뉴의 Run에서 사용하는 컴파일러를 변경할 수 있습니다. 

<img src ="/img/2021-02-24-java/chg_cmpler.png">