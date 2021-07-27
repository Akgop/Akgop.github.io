---
title: \[프로그래머스] 기능개발
categories: 
  - programmers
tags: 
  - queue
toc: true
---

### 큐, 브루트 포스

---

이 문제는 현재 작업 진행률에 해당하는 progresses, 하루에 수행할 수 있는 작업 진행 속도 speeds가 주어진다.

개발은 동시에 이루어지나, 앞의 기능이 개발이 완료된다면 그날 완료되어있는 뒤의 연속되어있는 모든 기능들과 함께 배포가 된다.

예를 들면 `[95 90 99 99 80 99], [1 1 1 1 1 1]` 인 경우 `[5 10 1 1 20 1]` 일 후에 각 작업은 완료된다.

그렇다면 5일 후에 1번째 작업 배포, 10일후에 2, 3, 4번째 작업 배포, 20일 후에 5, 6번째 작업을 배포하여 `[1, 3, 2]` 를 결과로 도출해야 하는 문제이다.

문제에서 주어진 인풋은 100 100 밖에 안되므로 그냥 완전탐색을 해서 구현해도 된다.

또한 큐(or deque), 리스트를 사용하여 head값을 이용하여 head보다 남은 일수가 큰 작업이 오면 카운팅 해주는 방식으로 구현할 수 있다.

코드는 다음과 같다.

### 구현

---

```python
import math

def solution(progresses, speeds):
    answer = []
    remaining_days = [0] * len(progresses)
    remaining_days[0] = math.ceil((100 - progresses[0]) / speeds[0])
    head = 0
    cnt = 1
    for i in range(1, len(progresses)):
        remaining_days[i] = math.ceil((100 - progresses[i]) / speeds[i])
        if remaining_days[head] >= remaining_days[i]:
            cnt += 1
            continue
        answer.append(cnt)
        head = i
        cnt = 1
    answer.append(cnt)
    return answer

#print(solution([93, 30, 55], [1, 30, 5]))
#print(solution([95, 90, 99, 99, 80, 99], [1, 1, 1, 1, 1, 1]))
#print(solution([95], [1]))
```