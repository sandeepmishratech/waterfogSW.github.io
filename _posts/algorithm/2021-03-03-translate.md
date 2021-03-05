---
title: "[Algorithm]3진법 뒤집기"
excerpt: 코딩테스트 연습 > 월간 코드 챌린지 시즌1 > 3진법 뒤집기

categories:
  - 'Algorithm'
tags:
  - 'Code Test'
  - 'Porgrammers'
  - 'Leet Code'
last_modified_at: 2021-03-02T08:06:00-05:00
toc: true
---

## Problem

자연수 n이 매개변수로 주어집니다. n을 3진법 상에서 앞뒤로 뒤집은 후, 이를 다시 10진법으로 표현한 수를 return 하도록 solution 함수를 완성해주세요.

## Constraints  

- n은 1 이상 100,000,000 이하인 자연수입니다.

## Examples

n|result
-|------
45|7
125|229

## Solution

### Java

```java
class Solution {
    public int solution(int n) {
        String tmp = "";
        while(n != 0){
            tmp += n % 3;
            n = n / 3;
        }
        int answer = Integer.parseInt(tmp,3);
        return answer;
    }
}
```

---

## Review  

라이브러리를 안써서 기쁘다 :)