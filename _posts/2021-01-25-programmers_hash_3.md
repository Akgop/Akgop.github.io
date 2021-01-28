---
title: \[프로그래머스] 위장
categories: 
  - programmers
tags: 
  - hash
toc: true
---

### 해시

---

문제에서 조합이라는 글만 보고 nCr을 가지고 장난쳐야 하는 문제라고 생각해서 itertools를 사용하여 combination을 써야 한다고 생각했다. 그렇게 한번 생각이 굳어버리니까 다른 방법은 떠오르지가 않았다.

한참을 고민한 뒤 아예 선택하지 않는 것 까지 선택지에 넣고 구한 후, 나온 결과값에 모두 선택하지 않는 경우를 -1을 해주는 방법이 떠올라서 아래와 같이 해결

```python
def solution(clothes):
    answer = 1
    categories = dict()
    for cloth in clothes:
        if cloth[1] in categories:
            categories[cloth[1]] += 1
        else:
            categories[cloth[1]] = 2
    value_list = list(categories.values())
    for cnt in value_list:
        answer *= cnt
    answer -= 1
    return answer
```