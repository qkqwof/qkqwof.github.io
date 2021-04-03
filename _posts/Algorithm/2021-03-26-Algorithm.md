---
layout: post
current: post
cover:  assets/built/images/algorithm.png
navigation: True
title: 주식가격 #
date: 2021-03-26 16:40:00 #
tags: [Algorithm] #태그명
class: post-template
subclass: 'post tag-Algorithm'
author: qkqwof #author.yml에서 설정한 author
---
## 주식가격 문제
![화면 캡처 2021-03-27 172824](https://user-images.githubusercontent.com/76687078/112715116-ef838a00-8f21-11eb-81f8-4c5f0874d13d.png)
## 문제 파악

- **0초일 때!**
가격 : prices[0] = 1
0초 이후에 prices보다
떨어질 때까지의 주식 : [2,3,2,3]
유지 시간 : 4초
- **1초일 때!**
가격 : prices[1] = 2
1초 이후 prices에서 2보다 떨어질 때까지의 주식 : [3, 2, 3]
유지 시간 : 3초
- **2초일 때!**
가격 : prices[2] = 3
2초 이후 prices에서 1보다 떨어질 때까지의 주식 : []
유지 시간 : 1초
- **3초일 때!**
가격 : prices[3] = 2
3초 이후 prices에서 1보다 큰 주식 가격 : [3]
유지 시간 : 1초
- **4초일 때!**
가격 : prices[4] = 3
4초 이후 prices에서 1보다 큰 주식 가격 목록 : []
유지 시간 : 0초
<br>
### 문제 풀이

시간이 지날 때마다, 스택에 저장된 마지막 시간의 주식 가격이 현재 주식 가격보다 작거나 같은 가격이 나올 때까지 스택에서 빼오고, 해당 유지 시간, **현재 - 마지막에 저장된 시간**을 answer의 마지막에 저장된 시간 위치에 저장

``` python
def solution(prices):
    n = len(prices)
    # answer를 prices 길이와 맞춘다.
    answer = [0] * n
    # 스택 생성
    st = []
    # 0 ~ n-1 초까지 순회
    for i in range(n):
        # 스택 비지 않고, prices[top] > prices[i] 이라면 반복
        while st and prices[st[-1]] > prices[i]:
            # 스택에서 마지막에 저장된 시간 top 꺼냄
            top = st.pop()
            # answer[top]에 i - top을 저장
            answer[top] = i - top
        # 스택에 현재 시간 i 저장
        st.append(i)
    
    # 만약 스택이 남아있다면, 스택이 빌 때까지 다음 반복
    while st:
        # 스택에서 마지막에 저장된 시간 top 꺼냄
        top = st.pop()
        # answer[top]에 가장 마지막 시간 n - i 에서 top을 뺸 시간 저장
        answer[top] = n - 1 - top
    
    return answer

print(solution([1,2,3,2,3]))
```