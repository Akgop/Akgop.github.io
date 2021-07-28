---
title: \[프로그래머스] 프린터
categories: 
  - programmers
tags: 
  - queue
toc: true
---

### 큐

---

해당 문제는 우선순위가 높은 순서대로 순차 프린트 하는 문제이다. 대기열의 첫 번째 우선순위보다 더 큰 우선순위를 갖는 값이 뒤에 존재한다면 이를 대기열의 가장 후순위에 다시 삽입한다는 조건이 있다.

이 조건을 통해 큐로 풀어야한다는 힌트를 얻을 수 있다.

다만 더 큰 우선순위를 탐색하는 방법에서 시간복잡도를 고려해보게 되는데, 다행히 인풋이 100밖에 안되서 그냥 주어진 조건대로 완전 탐색해보며 구현하면 되는 문제이다.

코드는 다음과 같다.

### 구현

---

```python
from collections import deque

def is_biggest(cur, q):
    for i in range(len(q)):
        if cur < q[i][0]:
            return False
    return True

def solution(priorities, location):
    answer = 1
    que = deque()
    for i in range(len(priorities)):
        que.append([priorities[i], i])
    while True:
        node, idx = que.popleft()
        if is_biggest(node, que):
            if idx == location:
                break
            answer += 1
            continue
        que.append([node, idx])
    return answer

#print(solution([2, 1, 3, 2], 2))
#print(solution([1, 1, 9, 1, 1, 1], 0))

```