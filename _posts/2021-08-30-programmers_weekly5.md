---
title: \[프로그래머스] 5주차 모음 사전
categories: 
  - programmers
tags:
  - bruteforce
toc: true
---
### 브루트 포스

---

해당 문제는 브루트 포스라고 볼 수 있다. 모음에 대한 모든 순열과 조합을 구해서 문자열이 몇 번째에 위치하는지 구하는 문제이다.

파이썬의 product 를 이용해서 중복 조합을 구할 수 있으나, 직접 DFS를 이용하여 구해보았다.

programmers 사이트는 입출력을 받지 않기 때문에 global 전역변수를 테스트 해볼 겸 해서 구현했다.

### 구현

---

```python
def backtrack(string, target, depth, max_depth, count, alpha):
    global answer
    answer += 1
    if string == target:
        return True
    if depth == max_depth:
        return False
    for i in range(5):
        if count[i] == 0:
            continue
        count[i] -= 1
        if backtrack(string + alpha[i], target, depth+1, max_depth, count, alpha):
            return True
        count[i] += 1
    return False

def solution(word):
    global answer
    count = [5, 5, 5, 5, 5]
    alpha = ['A', 'E', 'I', 'O', 'U']
    for i in range(5):
        count[i] -= 1
        if backtrack(alpha[i], word, 1, 5, count, alpha):
            break
        count[i] += 1
    return answer

answer = 0
```