---
title: \[프로그래머스] 다리를 지나는 트럭
categories: 
  - programmers
tags:
  - two pointer
  - queue
toc: true
---

### 투 포인터, 큐

---

이 문제는 트럭이 특정한 길이를 갖는 다리를 지나가는 문제이고, 다리가 감당할 수 있는 무게가 주어진다. 이에 따라 트럭이 전부 다 지나가는 시간을 측정하는 문제이다.

문제를 딱 보면 큐가 먼저 떠오른다. 그러나 queue로 구현 할 경우 은근히 복잡해진다. 왜냐하면 입장 시간, 큐의 길이 등 여러가지 고민할게 많아진다. 심지어 deque모듈을 사용해야 하므로 신경쓸게 많아진다.

따라서 직접 큐에 넣지 않고 큐 처럼 동작하게 하는 투 포인터 알고리즘을 사용할 수 있다. head와 tail을 선언하여 다리에서 빠져나가야 하는 트럭이 위치한 포인터를 tail, 다리에 입장해야 하는 트럭이 위치한 포인터를 head로 두고 풀면 된다.

투 포인터 알고리즘으로 구현할 경우 시간복잡도는 O(트럭의 개수 + 다리의 길이)가 된다.

신경써야 할 점은 head, tail의 IndexError, 그리고 연산의 순서이다

1. tail에서 빠져나가야할 트럭이있는지
2. 새로운 트럭이 들어올 수 있는지

위 순서를 지키지 않으면 특정 시간에 "동시에" 일이 일어나고 있는지 확인하기 어렵다. 코드는 다음과 같다.

### 구현

---

```python
def solution(bridge_length, weight, truck_weights):
    time, head, tail, cur_weight = 0, 0, 0, 0
    entry_time = [0] * len(truck_weights)
    while head < len(truck_weights):
        time += 1
        # 나가야 할 트럭이 있다면
        if entry_time[tail] + bridge_length == time:
            cur_weight -= truck_weights[tail]
            tail += 1
        if head >= len(truck_weights):
            continue
        # 트럭이 다리에 새로 입장해도 넘치지 않는다면
        if cur_weight + truck_weights[head] <= weight:
            entry_time[head] = time
            cur_weight += truck_weights[head]
            head += 1
    return time + bridge_length

# print(solution(10000, 10, [7, 4, 5, 6]))
# print(solution(100, 100, [10]))
# print(solution(100, 100, [10, 10, 10, 10, 10, 10, 10, 10, 10, 10]))
```