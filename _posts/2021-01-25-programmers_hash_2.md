---
title: \[프로그래머스] 전화번호 목록
categories: 
  - programmers
tags: 
  - hash
toc: true
---

### 1트

---

전원 통과 하지만 전부 시간초과

84.6 / 100 

```python
def solution(phone_book):   # 데이터 개수 100만
    answer = True
    cnt = 0
    for i in range(len(phone_book)):   # O(N)
        prefix = phone_book[i]
        for p in phone_book:           # O(N*N) = O(N^2) 
            if p.startswith(prefix):   # strncmp 와 유사
                print(p)
                cnt += 1
        if cnt > len(phone_book):
            answer = False
    return answer
```

그냥 startswith를 써서 그런것 같다.

해당 코드의 알고리즘은 O(N^2)으로 input이 100만이므로 1조의 연산을 하게 된다. 시간초과가 날 수 밖에..

### 2트

---

파이썬 sort연산의 특징을 이용하여 해결

문자열을 sort한다면 숫자의 "크기" 순서가 아니라 ascii기준 순서대로 정렬되므로
119, 11955, 1196  ← 이 순서로 정렬이 된다.

N은 100만이고, 해당 알고리즘은 시간복잡도가 sort에서 O(NlogN)

바깥 for문에서 O(N), 문자열의 최대 길이는 20이므로 startswith의 시간복잡도는 최대 20이다. 따라서 20*N정도인데 100만에 비하면 20은 작은 숫자이므로 그냥 O(N)으로 봐도 무방할듯 싶다.

따라서 정렬 알고리즘에 의해 성능이 좌우된다.

```python
def solution(phone_book):   # 데이터 개수 100만
    answer = True
    phone_book.sort()
    for i in range(1, len(phone_book)):
        if phone_book[i].startswith(phone_book[i-1]):
            answer = False
            break
    return answer
```