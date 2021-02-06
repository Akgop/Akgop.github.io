---
title: \[프로그래머스] 보석 쇼핑
categories: 
  - programmers
tags: 
  - hash
  - greedy
toc: true
---

### 해시, 그리디

---

해당 문제의 주어진 input은 10만이다. 효율성 검사의 기준은 모르지만, 10만의 제곱을 하면 시간초과가 날 것이라는 예상은 할 수 있었다. 따라서 O(N) ~ O(NlogN)근처의 알고리즘을 구현해야 한다는 생각을 했다.

해당 문제를 그리디라고 생각한 정당성은 다음과 같다.

- 보석은 무조건 연속으로 담는다

따라서, 배열을 읽는 두 개의 포인터를 전진시키며 구간을 계산하면 된다고 생각했다. 해당 포인터는 low와 high이다.

문제의 조건에서 모든 보석의 종류는 반드시 포함되어야 한다라는 조건이 있다. 여기서 종류 라는 단어의 의미는, 중복을 허용하지 않음을 의미한다. 따라서 해시 자료구조를 사용하여 탐색 속도를 높인다.

low와 high 인덱스 사이에, 모든 종류의 보석이 전부 있는지를 확인하기 위해서는 집합 자료형의 원소 개수와 딕셔너리 key의 개수가 동일하면 된다. 이를 주어진 list가 아니라 set과 dict을 사용한 이유는 해시 자료형은 key를 통한 탐색의 시간복잡도가 O(1)이기 때문이다. 

아래 코드가 중복 for문이므로 O(n^2)으로 생각할 수 있지만, 내부 for문의 조건이 low~high이고 low는 매 반복마다 +1 이 되므로 결국 2*O(N)이된다. 나머지 연산은 기본연산(primptive operation)이기 때문에 대세에 영향을 주지 않는다.

코드는 다음과 같다.

```python
def solution(gems):
    answer = []
    # in 메서드를 쓰기 위해 hash 구조로 저장
    gem_set = set()
    for gem in gems:
        gem_set.add(gem)
    # dict 자료구조로 현재 window 에 포함된 보석 개수 카운트
    gem_dict = dict()

    low, high = 0, 0
    for gem in gems:
        # gem 하나 넣고
        if gem in gem_dict:
            gem_dict[gem] += 1
        else:
            gem_dict[gem] = 1
        high += 1
        # 아직 모든 종류를 고르지 못했다면
        if len(gem_dict) < len(gem_set):
            continue
        # 모든 종류를 골랐다면 is_full == True
        # low 를 하나씩 당길 차례
        for i in range(low, high):
            # 앞의 gem 부터 뺀다.
            gem_dict[gems[i]] -= 1
            low += 1
            if gem_dict[gems[i]] == 0:
                if (high - low) < answer[2]:
                    answer = [low, high, high - low]
                gem_dict.pop(gems[i])
                break
    return answer[0:2]

print(solution(["DIA", "RUBY", "RUBY", "DIA", "DIA", "EMERALD", "SAPPHIRE", "DIA"]))
print(solution(["AA", "AB", "AC", "AA", "AC"]))
print(solution(["XYZ", "XYZ", "XYZ"]))
print(solution(["ZZZ", "YYY", "NNNN", "YYY", "BBB"]))
```