---
title: \[프로그래머스] 4주차 직업군 추천하기
categories: 
  - programmers
tags:
  - hash
  - simulation
toc: true
---

### 해시, 시뮬레이션

---

이 문제는 그냥 단순하게 주어진 내용을 어떠한 자료구조를 이용해서 정리하고 정답을 도출할 수 있는지, 문제 설계 능력을 보는 듯 했다. 사실 로직은 매우 간단하다.

사용자의 언어와 선호도와, 직업군마다 주어진 언어, 적합도를 이용하여 선호도 x 적합도를 하여 최대 값을 구하면 된다.

따라서 나는 이를 해시를 이용해서 숫자 - 문자열 맵핑을 하면 쉽게 풀릴 것이라 생각했다.

1. dict 배열을 5개 생성하고, 번호 - 직업이름을 맵핑해주는 배열 job을 선언한다. 그리고 최종 결과를 저장할 크기 5 짜리 배열을 선언한다.
2. language, preference 개수만큼 반복한다
3. dict 5개를 반복하며 language가 해당 직업에 있나 확인하고 있다면 preference 값 만큼 곱해서 최종 값을 저장하는 배열에 넣는다.
4. 제일 큰 값에 해당하는 값을 출력한다.

### 구현

---

```python
def solution(table, languages, preference):
    jobs = dict()
    score = [0] * 5
    points = [dict() for _ in range(5)]
    for i in range(len(table)):
        job, lang1, lang2, lang3, lang4, lang5 = table[i].split()
        jobs[i] = job
        points[i][lang1] = 5
        points[i][lang2] = 4
        points[i][lang3] = 3
        points[i][lang4] = 2
        points[i][lang5] = 1
    for i in range(len(languages)):
        for j in range(5):
            if languages[i] in points[j]:
                score[j] += preference[i]*points[j][languages[i]]
    answer = jobs[0]
    MAX = score[0]
    for i in range(len(score)):
        if score[i] == MAX:
            answer = min(jobs[i], answer)
        elif score[i] > MAX:
            MAX = score[i]
            answer = jobs[i]
    return answer
```