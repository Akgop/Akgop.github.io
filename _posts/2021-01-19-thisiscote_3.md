---
title: \[이코테] 3\. 구현
categories: 
  - thisiscote
tags: 
  - bruteforce
  - simulation
toc: true
---

### 관련 코드 구현

---

[[BOJ] 2290 LCD Test](http://akgop.github.io/boj/BOJ2290/)<br>
[[BOJ] 3568 iSharp](http://akgop.github.io/boj/BOJ3568/)<br>
[[BOJ] 14503 로봇 청소기](http://akgop.github.io/boj/BOJ14503/)<br>
[[BOJ] 14891 톱니바퀴](http://akgop.github.io/boj/BOJ14891/)<br>
[[BOJ] 15662 톱니바퀴2](http://akgop.github.io/boj/BOJ15662/)<br>
[[BOJ] 16918 봄버맨](http://akgop.github.io/boj/BOJ16918/)<br>
[[이코테] 실전 문제(예제)](http://akgop.github.io/thisiscote/thisiscote_31/)


### 개념

---

**풀이를 떠올리는 것은 쉽지만 소스코드로 옮기기 어려운 문제**

즉, 코딩 구현 능력 피지컬로 승부보는 문제들을 의미한다. 알고리즘 설계는 했는데, 구현하기가 어려운 문제들을 의미한다.

- 알고리즘은 간단한데 코드가 길어지는 문제
- 특정 소수점 자리까지 출력해야 하는 문제
- 문자열이 입력으로 주어졌을 때 한 문자 단위로 끊어서 리스트에 넣어야 하는(파싱을 해야 하는) 문제
- 사소한 조건 설정이 많은 문제

**완전 탐색(BruteForce)**

- 모든 경우의 수를 주저없이 다 계산하는 해결 방법

**시뮬레이션 유형**

- 문제에서 제시한 알고리즘을 한 단계씩 차례대로 직접 수행해야 하는 문제 유형

API 개발 문제

- 문제풀이 서버와 통신하는 프로그램 모듈 작성하는 문제 유형

### 구현 시 고려해야 할 메모리 제약 사항

---

C/C++, Java는 자료형을 명시해야 하고 주로 int형을 사용한다. 이는 4byte이다.

다만 python을 사용하는 경우에는 직접 자료형을 명시할 필요가 없고, 매우 큰 수의 연산 또한 기본으로 지원하기 때문에 깊게 이해할 필요는 없지만 알아두자

리스트의 경우 메모리 제한에 근접하도록 출제되는 경우는 적으니 이점을 유의하고 만약 메모리 초과라면 잘못 구현했을 가능성이 높다

파이썬의 경우 **1초에 2000만번**의 연산을 수행한다고 가정하고 문제를 풀면 시간제한에 안정적이다.

따라서 시간 제한과 데이터의 개수를 먼저 확인한 뒤에 이 문제를 어느정도의 시간 복잡도의 알고리즘으로 작성해야 풀 수 있을 것인지를 예측해야 한다.

- 시간제한 1초, 데이터 100만개 → O(NlogN)

### 예제

---

책의 111페이지 예제에 대한 코드

```python
import sys
N = int(sys.stdin.readline())
commands = list(map(str, sys.stdin.readline().split()))

x, y = 1, 1
for cmd in commands:
    if cmd == 'R':
        if y == N:
            continue
        y += 1
    elif cmd == 'L':
        if y == 1:
            continue
        y -= 1
    elif cmd == 'U':
        if x == 1:
            continue
        x -= 1
    else:
        if x == N:
            continue
        x += 1
print(x, y)
```

단순한 if ~ else 구문의 코드와 예외처리들에 대한 내용이다. 

113페이지 예제

```python
N = int(input())

answer = 0
for i in range(N+1):
    if '3' in str(i):
        answer += 3600
        continue
    for j in range(60):
        if '3' in str(j):
            answer += 60
            continue
        for k in range(60):
            if '3' in str(k):
                answer += 1
print(answer)
```

시간을 모두 초로 바꿔도 24\*60\*60 = 86400으로 연산 수가 많지 않다. 따라서 3중 반복문을 이용한 완전 탐색을 통해 연산해도 된다. 
