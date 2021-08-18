---
title: \[프로그래머스] 1주차 부족한 금액 계산하기
categories: 
  - programmers
tags:
  - math
  - bruteforce
toc: true
---

### 수학

---

이 문제는 시그마N의 수식을 이용해서 푸는 문제이다.

count 만큼 반복하며 N 배씩 커진 가격을 더하는 문제인데, price로 묶으면 내부의 count 값이 그냥

1+2+3+...+n-1+n 과 같이 된다.

마지막에 0의 조건만 조심해서 return 하면 된다.

### 구현

---

```python
def solution(price, money, count):
    answer = ((count * (count + 1)) // 2) * price
    return 0 if money >= answer else (answer - money)
```