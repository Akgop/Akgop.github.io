---
title: \[이코테] 4. DFS/BFS
categories: 
  - thisiscote
tags: 
  - dfs
  - bfs
toc: true
---

### 관련 코드 구현

---

[[BOJ] 1012 유기농 배추](http://akgop.github.io/boj/BOJ1012/)<br>
[[BOJ] 1707 이분 그래프](http://akgop.github.io/boj/BOJ1707/)<br>
[[BOJ] 2206 벽 부수고 이동하기](http://akgop.github.io/boj/BOJ2206/)<br>
[[BOJ] 2606 바이러스](http://akgop.github.io/boj/BOJ2606/)<br>
[[BOJ] 2667 단지 번호 붙이기](http://akgop.github.io/boj/BOJ2667/)<br>
[[BOJ] 5567 결혼식](http://akgop.github.io/boj/BOJ5567/)<br>
[[BOJ] 5567 결혼식](http://akgop.github.io/boj/BOJ5567/)<br>
[[BOJ] 7562 나이트의 이동](http://akgop.github.io/boj/BOJ7562/)<br>
[[BOJ] 7569 토마토](http://akgop.github.io/boj/BOJ7569/)<br>
[[BOJ] 14502 연구소](http://akgop.github.io/boj/BOJ14502/)<br>
[[이코테] 실전 문제(예제)](http://akgop.github.io/thisiscote/thisiscote_41/)


### 선행 개념

---

DFS와 BFS를 구현하기 위해서 선행되는 개념이 몇 가지 있다

**스택(Stack)**: 나중에 넣은 데이터를 먼저 꺼내는 자료구조. 그냥 리스트를 사용하여 append, pop 메서드를 사용하여 구현한다.

**큐(Queue)**: 먼저 넣은 데이터를 먼저 꺼내는 자료구조. **큐는 collections 모듈의 deque를 사용하여 구현한다.**

**재귀 함수(Recursive function)**: 자기 자신을 다시 호출하는 함수. **재귀 함수를 문제 풀이에서 사용할 때는 재귀 함수가 언제 끝날지, 종료 조건을 꼭 명시해야 한다.**

재귀함수가 반복문보다 유리한점은 코드가 간결해진다는 것이다.

**노드(Node), 정점(Vertex)**: 그래프에서 하나의 점

**간선(Edge)**: 점과 점을 잇는 연결 선

인접(Adjacent): 두 노드가 간선으로 직접적으로 연결되어있다면 인접하다 라고 표현. 해당 내용을 보통 인접 행렬(2차원 배열), 인접 리스트(1차원 배열==리스트)로 표현한다.

인접 행렬(Adjacency Matrix): 2차원 리스트에 각 노드가 연결된 형태를 기록한다.

인접 리스트(Ajacency List): 1차원 리스트에 인접 노드들을 링크드 리스트 방식으로 연결하여 저장하는 것

```python
0 → (1, 7) → (2, 5)   # 0번 노드에 인접한 (노드, 가중치)
1 → (0, 7)  # Linked List형식으로 저장
2 → (0, 5)  # Python 에서는 list자료형의 append, pop 이용

# 위 내용 코드로 작성
graph = [[] for _ in range(3)]

graph[0].append((1, 7))
graph[0].append((2, 5))
graph[1].append((0, 7))
graph[2].append((0, 5))
```

인접 리스트 방식이 인접 행렬 방식에 비해 메모리 측면에서 효율적이다. 필요한 자료만 저장하기 때문

인접 행렬 방식이 인접 리스트 방식에 비해 시간 측면에서 효율적이다. 리스트는 연결된 데이터를 순차 탐색 해야 하기 때문, 인접 행렬 방식은 인덱싱이 가능하기 때문

### DFS(Depth First Search)

---

DFS는 스택 자료구조를 이용하며 동작 과정은 다음과 같다

1. 탐색 시작 노드를 스택에 삽입 → 방문 처리(visited = True)
2. 스택의 최상단 노드(현재 위치하고 있는 노드)의 인접 노드중, 방문하지 않은 인접 노드가 있으면 해당 노드를 스택에 넣고 방문 처리(인접 노드로 현재 노드를 바꿔줌). 
방문하지 않은 인접 노드가 없으면 스택에서 최상단 노드를 꺼낸다
3. 2를 수행할 수 없을 때까지 반복

인접 노드가 여러개라면 노드 번호가 낮은것 부터 처리한다.

DFS는 스택 자료구조를 사용하므로 재귀로 간단하게 구현이 가능하다.

탐색 기준 데이터의 개수가 N개인 경우 O(N)의 시간이 소요된다는 특징이 있다.탐색 기준 데이터의 개수가 N개인 경우 O(N)의 시간이 소요된다는 특징이 있다.

```python
def dfs(graph, v, visited):
    # 현재 노드 방문 처리
    visited[v] = True
    print(v, end=' ')
    # 인접 노드 탐색
    for i in graph[v]:   # for 문을 통해 노드 번호가 낮은 노드부터
        # 방문하지 않은 노드가 있다면
        if not visited[i]:
            # 해당 노드로 이동
            dfs(graph, i, visited)

# 인접 리스트로 초기화
graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

visited = [False] * 9

dfs(graph, 1, visited)

>>> 1 2 7 6 8 3 4 5
```

기본적인 DFS의 예시는 다음과 같다.

### BFS(Breath First Search)

---

너비 우선 탐색, 즉 가까운 노드부터 탐색하는 알고리즘

DFS는 인접노드를 타고 계속 안으로 파고들어가는 방식이라면 BFS는 주변 인접 노드를 다 탐색하고 그 다음 층으로 이동하는 방식이라고 생각하면 된다.

큐 자료구조를 이용한다. 인접한 노드들을 반복적으로 큐에 넣도록 작성하면 자연스럽게 먼저 들어온 인접 노드들이 먼저 처리된다.

1. 시작 노드를 큐에 삽입 → 방문 처리
2. 큐에서 노드를 꺼낸다 → 꺼낸 노드의 인접 노드중 방문하지 않은 노드를 모두 큐에 삽입, 방문처리 (큐에 삽입함과 동시에 방문 처리를 하는 것)
3. 2를 수행할 수 없을 때까지 반복

deque라이브러리를 사용하는 것이 좋다

탐색 기준 데이터의 개수가 N개인 경우 O(N)의 시간이 소요된다는 특징이 있다.

컴퓨터 시스템은 함수 호출(Function Call)에 대한 오버헤드가 있어서 주로 BFS가 DFS보다는 속도가 빠른 편이다.

```python
from collections import deque

def bfs(graph, start, visited):
    queue = deque([start])
    visited[start] = True
    while queue:
        v = queue.popleft()
        print(v, end=' ')
        for i in graph[v]:
            if not visited[i]:
                queue.append(i)
                visited[i] = True

# 인접 리스트로 초기화
graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

visited = [False] * 9

bfs(graph, 1, visited)
```

코딩 테스트에서 2차원 배열에서의 탐색 문제를 만나면 그래프 형태로 바꿔서 생각하면 풀이 방법을 조금 더 쉽게 떠올릴 수 있다.