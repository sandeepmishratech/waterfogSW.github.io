---
title: "[Algorithm]가장 긴 팰린드롬"
excerpt: 코딩테스트 연습 > 연습문제 > 가장 긴 팰린드롬

categories:
  - 'Algorithm'
tags:
  - 'Porgrammers'
last_modified_at: 2021-03-04T08:06:00-05:00
toc: true
---

## Problem

앞뒤를 뒤집어도 똑같은 문자열을 팰린드롬(palindrome)이라고 합니다.
문자열 s가 주어질 때, s의 부분문자열(Substring)중 가장 긴 팰린드롬의 길이를 return 하는 solution 함수를 완성해 주세요.

예를들면, 문자열 s가 "abcdcba"이면 7을 return하고 "abacde"이면 3을 return합니다.

## Constraints  

- 문자열 s의 길이 : 2,500 이하의 자연수
- 문자열 s는 알파벳 소문자로만 구성

## Examples

s|result
-|------
"abcdcba"|7
"abacde"|3

## Solution

### C++

```c++
#include <iostream>
#include <string>
using namespace std;
int solution(string s)
{
    int s_len = s.length();
    if(s_len < 2) return s_len;
        
    int max_len = 1;
    for(int i = 0; i < s_len; i++){
        int begin = i;
        while(s[i] == s[i+1]) i++;
        int end = i;
        while(begin>0 && end<s_len-1 && s[begin-1]==s[end+1]){
            begin--;
            end++;
        }
        int cur_len = end-begin+1;
        if(cur_len>max_len) max_len = cur_len;
    }

    return max_len;
}
```

### Java

```java
class Solution
{
    public int solution(String s)
    {   
        int len = s.length();
        if(len < 2) return len;
        
        int max_len = 1;
        for(int i = 0; i < len; i++){
            int begin = i;
            while(i+1 < len &&s.charAt(i) == s.charAt(i+1)) i++;
            int end = i;
            while(begin>0 && end<s.length()-1 && s.charAt(begin-1)==s.charAt(end+1)){
                begin--;
                end++;
            }
            int cur_len = end-begin+1;
            if(cur_len>max_len) max_len = cur_len;
        }
        return max_len;
    }
}
```

---

## Review  

과거에 leetcode에서 비슷한 문제를 풀어봐서 아이디어는 쉽게 생각할 수 있었다. 문자열을 순회하면서 해당 문자 양옆을 포함해 나가면서 회문인지 판별해 나가면 loop를 줄일수 있었다. 주의할 점은 문자가 중복되는 경우와 반복 조건을 잘 설정하여 out of range와 같은 에러가 발생하는것을 잘 생각해야 한다. 이부분에서 JAVA에 비해 C++이 조금 관대하다는 생각이 들었는데 JAVA의 경우 charAt을 통해 문자열을 참조하면서 범위에서 벗어나면 얄짤없이 String index out of range 오류를 출력한다.