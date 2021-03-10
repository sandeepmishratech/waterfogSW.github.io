---
title: "[Algorithm]더 맵게"
excerpt: 코딩테스트 연습 > 2017 팁스타운 > 짝지어 제거하기

categories:
  - 'Algorithm'
tags:
  - 'Porgrammers'
last_modified_at: 2021-03-10T08:06:00-05:00
toc: true
---

## Problem

매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다. 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.

```
섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 * 2)
```

Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때, 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.

## Constraints  

- scoville의 길이는 2 이상 1,000,000 이하입니다.
- K는 0 이상 1,000,000,000 이하입니다.
- scoville의 원소는 각각 0 이상 1,000,000 이하입니다.
- 모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.

## Examples

scoville|k|return
-|------|-----
[1, 2, 3, 9, 10, 12]|7|2


## Solution

### Java

우선순위 큐 사용

```java
import java.util.PriorityQueue;
class Solution {
    public int solution(int[] scoville, int K) {
        int answer = 0;
        PriorityQueue<Integer> res = new PriorityQueue<>();
        for(int i:scoville) res.add(i);
        while(res.peek() < K && res.size() > 1){
            res.add(res.remove() + res.remove() * 2);
            answer++;
        }
        if(res.peek() < K) return -1;
        return answer;
    }
}
```

---

## Review  

힙이 떠오르긴 했는데 자바 컨테이너 종류에 대해 많이 알지 못해서 우선 순위 큐 컨테이너가 따로 있는줄 몰랐다. ArrayList로 삽질하다가 풀어냈다.