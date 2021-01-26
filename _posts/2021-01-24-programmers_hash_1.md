---
title: \[프로그래머스] 완주하지 못한 선수
categories: 
  - programmers
tags: 
  - hash
  - dictionary
---

### 1트

---

```python
def solution(participant, completion):
    answer = ''    
    for p in participant:
        if p not in completion:
            answer = p
            break
    return answer
```

결과는 30/100 

효율성 에서 0점, 구현에서 30점

우선 구현에서 같은 명단이 있을때 

```python
if p not in completion
```

여기서 판단을 못해준다.

### 2트

---

해시유형의 문제는 딕셔너리등 내부가 해시 구조인 자료형을 활용 하는 것

hashlib을 써서 구현해야 하나 고민했는데 그냥 딕셔너리 내부가 해시로 동작한다는 것만 기억하면 되는 쉬운 문제였음

그래서 dictionary에 이름을 key값으로 두고, +1 -1만 하면서 구현하는 문제였음

그리고 남은 값 하나를 반환해서 출력하면 끝

```python
def solution(participant, completion):
    answer = ''
    hash_table_p = dict()
    for p in participant:
        if p in hash_table_p:
            hash_table_p[p] += 1
        else:
            hash_table_p[p] = 1
    for c in completion:
        if c in hash_table_p:
            hash_table_p[c] -= 1
            if hash_table_p[c] == 0:
                hash_table_p.pop(c)
    answer = list(hash_table_p.keys())
    return answer[0]
```