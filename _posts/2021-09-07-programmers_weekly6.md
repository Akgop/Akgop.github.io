---
title: \[프로그래머스] 6주차 복서 정렬하기
categories: 
  - programmers
tags:
  - sort
toc: true
---

### 정렬

---

이 문제는 주어진 조건에 의해 정렬하면 된다. 알맞게 리스트를 만들고 람다 식을 통해 정렬하였다.

리스트의 자료 구조는 [승률, 이긴 횟수, 몸무게, 인덱스 번호] 로 구현하면 된다.

람다식을 쓰기 싫다면, 인덱스 번호를 음수로 저장하여 `sort(reverse=True)` 옵션을 주면 똑같은 결과가 나올 것이다.

### 구현

---

```python
def solution(weights, head2head):
    answer = []
    n = len(weights)
    result = []
    for i in range(n):
        win_cnt, win_cnt_heavier, total_game = 0, 0, 0
        for j in range(n):
            fight = head2head[i][j]
            if fight == 'N':
                continue
            if fight == 'L':
                total_game += 1
                continue
            if fight == 'W':
                if weights[i] < weights[j]:
                    win_cnt_heavier += 1
                win_cnt += 1
                total_game += 1
        win_rate = (win_cnt / total_game) if total_game != 0 else 0
        result.append([win_rate, win_cnt_heavier, weights[i], i+1])
    result.sort(key=lambda x: (-x[0], -x[1], -x[2], x[3]))
    for r in result:
        answer.append(r[3])
    return answer
```