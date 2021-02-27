---
title: "[Code Test]두 개 뽑아서 더하기"
excerpt: Programmers > 월간 코드 챌린지 시즌1 > 두 개 뽑아서 더하기
categories:
  - "Code Test"
tags:
  - 'Code Test'
  - 'Porgrammers'
  - 'Leet Code'
last_modified_at: 2021-02-27T08:06:00-05:00
toc: true
---
## Problem  
정수 배열 numbers가 주어집니다. numbers에서 서로 다른 인덱스에 있는 두 개의 수를 뽑아 더해서 만들 수 있는 모든 수를 배열에 오름차순으로 담아 return 하도록 solution 함수를 완성해주세요.  

## Constraints  
- numbers의 길이는 2 이상 100 이하입니다.
- numbers의 모든 수는 0 이상 100 이하입니다.  

## Examples
<table style="text-align:center">
    <tr><td>numbers</td><td>result</td></tr>
    <tr><td>[2,1,3,4,1]</td><td>[2,3,4,5,6,7]</td></tr>
    <tr><td>[5,0,2,7]</td><td>[2,5,7,9,12]</td></tr>
</table>
<hr>

## Solution
- 오름차순 -> 정렬
- 효율적인 배열 순회
- 중복 결과값 제거

### Python
```python
def solution(numbers):
    answer = []
    for i in range(len(numbers)):
        for j in range(i+1,len(numbers)):
            answer.append(numbers[i] + numbers[j])
    answer = list(set(answer))
    answer.sort()
    return answer
```

### C++
```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> solution(vector<int> numbers) {
    vector<int> answer;
    for(int i = 0; i < numbers.size(); i++){
        for(int j = i+1; j < numbers.size(); j++){
            answer.push_back(numbers[i]+numbers[j]);
        }
    }
    sort(answer.begin(), answer.end());
    answer.erase(unique(answer.begin(), answer.end()),answer.end());
    return answer;
}
```

### Java
```java
import java.util.ArrayList;
import java.util.Collections;
class Solution {
    public ArrayList<Integer> solution(int[] numbers) {
        ArrayList<Integer> answer = new ArrayList<>();
        for(int i = 0; i < numbers.length; i++){
            for(int j = i+1; j < numbers.length; j++){
                if(!answer.contains(numbers[i]+numbers[j]))
                    answer.add(numbers[i]+numbers[j]);
            }
        }
        Collections.sort(answer);
        return answer;
    }
}
```

<hr>

## Review
STL, 라이브러리에 너무 의존적인것 같다... 또, 3언어 모두 for문을 중첩하여 사용하게 되었는데 이를 해결할 방법이 loop병렬화 외에는 딱히 떠오르지 않았다. 추후에 공부하면서 아이디어가 있으면 수정해야겠다. 
