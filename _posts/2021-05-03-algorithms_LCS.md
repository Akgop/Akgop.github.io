---
title: \[알고리즘] LCS(Longest Common Subsequence) 최장 공통 부분 수열
categories: 
  - algorithms
tags: 
  - dynamic programming
  - longest common subsequence
toc: true
---

### LCS란?

---

LCS란 Longest Common Subsequence의 줄임말로, 최장 공통 부분 수열이다. LCS는 Longest Common Substring도 존재하는데 둘의 차이점을 알아보자. substring은 연속된 부분 문자열이고, subsequence는 연속과는 무관한 부분 문자열이다. 즉 사이사이 공백이 있어도 된다.

본 포스트에서는 LCS중 Subsequence에 대한 내용만 다룬다.

![LCS1.jpeg](/assets/images/algorithms/LCS1.jpeg)

LCS의 길이 구하기 알고리즘은 Dynamic Programming의 대표적인 유형이다.

### 구현(Python)

---

DP를 사용한 점화식은 다음과 같다.

- if str1 == str2 → f(i, j) = f(i-1, j-1) + 1

![LCS2.jpeg](/assets/images/algorithms/LCS2.jpeg)

- if str ≠ str2 → f(i, j) = max(f(i-1, j), f(i, j-1)

![LCS3.jpeg](/assets/images/algorithms/LCS3.jpeg)

전부 구현이 완료되면 아래와 같다.

![LCS4.jpeg](/assets/images/algorithms/LCS4.jpeg)

위 내용대로 구현하면 아래와 같다. 

```python
table = [[0]*(len(str2)+1) for _ in range(len(str1)+1)]

for i in range(1, len(str1)+1):
    for j in range(1, len(str2)+1):
        if str1[i-1] == str2[j-1]:
            table[i][j] = table[i-1][j-1] + 1
        else:
            table[i][j] = max(table[i-1][j], table[i][j-1])
```

처음 table을 초기화 할 때 +1을 해주는 것은 i-1, j-1 에 대한 인덱스 범위를 컨트롤하기에 편하기 때문에 한 줄 더 부여한다.

위 코드는 2차원 table을 사용했지만 메모리 관리의 측면에서 1차원 메모리를 두고 구현할 수 있다. 추후에 알아보자.