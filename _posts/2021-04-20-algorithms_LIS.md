---
title: \[알고리즘] LIS(Longest Incresing Subsequence) 최장 증가 부분수열
categories: 
  - algorithms
tags: 
  - dynamic programming
  - longest increasing subsequence
toc: true
---

### LIS란?

---

최장 증가 부분 수열을 의미한다.

원소가 n개인 배열의 일부 원소를 골라내서 만든 부분 수열 중, 각 원소가 이전 원소보다 "크다" 는 조건을 만족하고, 부분 집합의 크기가 가장 큰 경우를 최장 증가 부분 수열 이라고 한다.

자세한 내용은 아래와 같다.

![LIS0.jpeg](/assets/images/algorithms/lis0.jpeg)

주어진 수열에서 { (4 또는 2), 5, 8, 11, 15 } 를 고르게 되면 총 길이 5의 LIS가 나오게 된다. 다른 부분 수열들도 존재하지만 최장 길이를 갖는 부분수열은 위 5개를 고르는 경우이다. 

### 구현 (Python)

---

일반적으로 LIS를 구현하는 방법은 다이나믹 프로그래밍 알고리즘을 이용하는 것이다.

![LIS1.jpeg](/assets/images/algorithms/lis1.jpeg)

간단한 개념은 위와 같다. 

- i 를 증가시키며 배열을 순차 탐색
- f(i) 와 f(j) 를 비교
- f(i)가 더 크다면 점화식 수행 → f(i) = max( f(i), f(j) + 1 )

2중 반복문을 사용하여 구할 수 있다. 코드는 다음과 같다. n은 전체 수열의 길이, 즉 전체 집합의 크기이다.

```python
def lis(n):
    result = [1] * n
    for i in range(n):
        for j in range(i):
            if li[i] > li[j]:   # 현재 값이 이전 값보다 클 경우
                result[i] = max(result[i], result[j] + 1)
```

### 시간 복잡도 개선

---

이분 탐색을 이용하여 O(NlogN)으로 개선할 수 있다.

자세한 내용은 추후에 추가할 예정이다.