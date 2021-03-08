---
title: \[이코테] 9. 그래프 이론
categories: 
  - thisiscote
tags: 
  - graph
  - union-find
  - kruskal
  - topology sort
toc: true
---

앞서 배운 DFS/BFS, 최단 경로 에서 다룬 내용 모두 그래프 알고리즘의 한 유형이다.

### 그래프(Graph)

---

그래프란 노드(Node)와 간선(Edge)의 정보를 갖고 있는 자료구조를 의미한다. 즉, 코딩 테스트에서 여러 개의 도시가 연결되어 있다와 같은 내용이 등장하면 그래프 알고리즘을 의심해보자.

트리(Tree) 자료구조 또한 그래프 자료구조에 속하고,  다양한 알고리즘에서 자주 사용된다.

*트리, 그래프 비교 표 첨부하기*

앞서 DFS/BFS에서 보았던 것 처럼 그래프를 구현하는 방법은 2가지가 있다.

- 인접 행렬(Adjacency Matrix): 2차원 배열 사용, 공간: O(V^2), 시간: O(1), 플로이드
- 인접 리스트(Adjacenty List): 리스트를 사용하는 방식, 공간: O(E), 시간: O(V), 다익스트라(min-heap)

### 서로소 집합(Disjoint Sets), 유니온-파인드(Union-Find)

---

서로소 집합이란 공통 원소가 없는 두 집합을 의미한다. 서로조 집합 자료구조란 서로조 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조이다. Union, Find 두 개의 연산으로 조작할 수 있다.

서로소 집합 자료구조를 구현하기 위해서는 1개의 배열과 2가지 연산이 필요하다. 1개의 배열은 각 노드의 부모 노드 정보를 저장하기 위한 부모 테이블, 2개의 연산은 각각 union, find이다.

서로소 집합 자료구조를 구현할 때는 트리 자료구조를 이용하여 표현한다.

Union연산은 2개의 원소가 포함된 집합을 하나의 집합으로 합치는 연산이다. 즉, 어떤 노드의 부모 노드를 다른 하나로 지정하는 것이다.

Find 연산은 특정한 원소가 속한 집합이 어떤 집합인지 알려주는 연산이다. 즉, 해당 노드의 루트 노드를 배열에서 재귀적으로 찾아가는 연산이다.

- Union연산을 확인하여 서로 연결된 두 노드 A, B를 찾는다.각각의 루트 노드 A', B'를 찾는다. 여기서 A'를 B'의 부모노드로 설정한다.(B'가 A'를 가리키도록 한다). 보통 더 큰 루트 노드가 더 작은 루트 노드를 가리키도록 한다.
- 모든 Union을 처리할 때까지 위 과정을 반복한다.

이 알고리즘에서 유의할 점은 우리는 union연산을 효과적으로 수행하기 위해 부모 테이블을 항상 갖고 있어야 한다는 점이다. 또한, 루트 노드를 즉시 계산할 수 없고 부모 테이블을 계속해서 확인하며 거슬러 올라가야 한다(더 빠르게 하는 방법은 아래에서 추가로 설명)

```python
# find 연산: 특정 원소가 속한 집합을 찾기
def find_parent(parent, x):
    # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if parent[x] != x:
        return find_parent(parent, parent[x])
    return x

# union 연산: 두 원소가 속한 집합을 합치기, 두 서브 트리 합치기
def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# 노드의 개수와 간선의 개수 입력받기
v, e = map(int, input().split())
parent = [0] * (v + 1)      # 부모 테이블 초기화

# 부모 테이블상에서, 부모를 자기 자신으로 초기화
for i in range(1, v + 1):
    parent[i] = i

# union 연산을 각각 수행
for i in range(e):
    a, b = map(int, input().split())
    union_parent(parent, a, b)

# 각 원소의 루트 노드 출력 (집합 구분)
print("각 원소가 속한 집합(루트 노드): ", end='')
for i in range(1, v + 1):
    print(find_parent(parent, i), end=' ')

print()
# 부모 테이블 출력
print("부모 테이블: ", end='')
for i in range(1, v+1):
    print(parent[i], end=' ')
```

위 코드와 같이 구현하면 최악의 경우 find함수가 모든 노드를 다 확인하느라 O(V)의 시간이 소요된다.

find함수는 경로 압축(Path Compression)기법을 적용하면 개선할 수 있다. find함수를 재귀적으로 호출한 뒤에, 부모 테이블 값을 갱신하는 기법이다.

```python
# find 연산: 특정 원소가 속한 집합을 찾기 - 경로 압축법(Path Compression)
def find_parent(parent, x):
    # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]
```

따라서 경로 압축법을 이용한, 서로소 집합 알고리즘의 시간 복잡도는 $O(V + M(1 + log_{2-M/V}V))$가 된다.

### 서로소 집합을 이용한 사이클 판별

---

서로소 집합은 무방향 그래프 내에서의 사이클을 판별할 때 사용할 수 있다는 특징이 있다. 방향그래프에서의 사이클 여부는 DFS를 이용하여 판별할 수 있다.

방법은 다음과 같다.

- 간선을 확인하며 두 노드의 루트 노드를 확인한다.
- 루트 노드가 서로 다르다면 두 노드에 대해 union 연산을 수행
- 루트 노드가 서로 같다면 사이클이 발생한 것이다.

그래프에 포함되어 있는 모든 간선에 대하여 위를 반복한다.

```python
# 앞서 구현한 find_parent, union_parent 함수 사용
# 사이클 발생 여부 flag
cycle = False

for i in range(e):
    a, b = map(int, input().split())
    # 사이클이 여부 판별 -> 발생시 종료
    if find_parent(parent, a) == find_parent(parent, b):
        cycle = True
        break
    else:       # 발생하지 않았다면 union 연산 수행
        union_parent(parent, a, b)

if cycle:
    print("사이클 발생")
else:
    print("사이클 x")
```

### 신장 트리(Spanning Tree)

---

**신장 트리**란 하나의 그래프가 있을 때 **모든 노드를 포함**하면서, **사이클이 존재하지 않는**, **부분 그래프**를 의미한다.

**최소 신장 트리(MST, Minimum Spanning Tree)**란 신장 트리 중, 가장 비용이 적은 트리를 뜻한다. 최소 신장 트리를 구하기 위해서는 크루스칼 알고리즘이 있다.

### 크루스칼(Kruskal) 알고리즘

---

크루스칼 알고리즘을 사용하면 가장 적은 비용으로 모든 노드를 연결할 수 있다. 크루스칼 알고리즘은 그리디 알고리즘으로 분류된다. 모든 간선에 대하여 정렬을 수행한 뒤에 가장 거리가 짧은 간선부터 집합에 포함시키면 된다.사이클을 발생시킬 수 있는 간선의 경우 집합에 포함시키지 않는다. 알고리즘은 다음과 같다.

- 간선 데이터를 비용에 따라 오름차순 정렬
- 현재의 간선이 사이클을 발생시키는지 확인, 앞서 배운 Union 연산을 통해 알아볼 수 있다.
- 발생하지 않는 경우 MST에 포함, 발생하는 경우 건너뜀

최소 신장 트리에 포함되는 간선의 개수는 노드의 개수 -1 과 같다는 특징이 있다.

```python
import sys

# find 연산 - 경로 압축법
def find_parent(parent, x):
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

# union 연산
def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# 기본 입력
v, e = map(int, sys.stdin.readline().rstrip().split())
parent = [0] * (v + 1)

# 모든 간선을 담을 리스트와 최종 비용을 담을 변수
edges = []
result = 0

# 부모 테이블 루트 노드 자기 자신으로 초기화
for i in range(1, v + 1):
    parent[i] = i

# 기본 입력
for _ in range(e):
    a, b, cost = map(int, sys.stdin.readline().rstrip().split())
    # 비용 순으로 edge 정렬하기 위해 튜플의 첫 번째 원소를 비용으로 설정
    edges.append((cost, a, b))

# 간선을 비용 순으로 정렬
edges.sort()

# 간선을 하나씩 확인
for edge in edges:
    cost, a, b = edge
    # 사이클이 발생하지 않는 경우에만 집합에 포함
    if find_parent(parent, a) != find_parent(parent, b):
        union_parent(parent, a, b)
        result += cost

print(result)
```

크루스칼 알고리즘은 간선의 개수가 E개 일때, O(ElogE)의 시간 복잡도를 갖는다. 왜냐하면 크루스칼 알고리즘에서 시간이 가장 오래 걸리는 부분이 간선을 정렬하는 작업이다. 크루스칼 내부에서 사용하는 서로소 집합 알고리즘의 시간 복잡도는 정렬 알고리즘의 시간 복잡도보다 작으므로 무시한다.

### 위상 정렬(Topology Sort)

---

**위상 정렬**은 순서가 정해져 있는 일련의 작업을 차례대로 수행해야 할 때 사용할 수 있는 알고리즘이다. 자세하게 보자면, 방향 그래프의 모든 노드를 방향성에 거스르지 않도록 순서대로 나열하는 것이다. 예를 들어 설명하면 이해하기 좋다.

예를 들어 '선수과목을 고려한 학습 순서 설정', '게임 스킬 연계'등이 있다. 자료구조를 수강해야만 알고리즘 수업을 들을 수 있을 때 자료구조, 알고리즘을 각각의 노드로 표현하고, 이를 이어지는 방향성을 갖는 그래프로 표현할 수 있다. 즉, 다시 말해서 그래프 상에서 선후 관계까 있다면, 위상 정렬을 수행하여 모든 선수 관계를 지키는 전체 순서를 계산할 수 있다.

**진입차수(Indegree)**란 특정한 노드로 들어오는 간선의 개수를 의미한다. 반대로 나가는건 **출력차수(outdegree)**라고 한다.

위상 정렬 알고리즘은 다음과 같다.

- 진입차수가 0인 노드를 큐에 넣는다.
- 큐가 빌 때까지 아래를 반복
- 큐에서 원소를 꺼내 해당 노드에서 출발하는 간선을 그래프에서 제거
- 새롭게 진입차수가 0이 된 노드를 큐에 넣는다.

이때 모든 원소를 방문하기 전에 큐가 빈다면 사이클이 존재한다고 판단할 수 있다. 다시 말해서 큐에서 원소가 V번 추출되기 전에 큐가 비어버리면 사이클이 발생한 것이다. 다만, 기본적으로 위상 정렬 문제에서는 사이클이 발생하지 않는다고 명시하는 경우가 더 많으므로 알아두자

```python
from collections import deque
import sys

# 노드의 개수, 간선의 개수 입력받기
v, e = map(int, sys.stdin.readline().rstrip().split())
# 모든 노드에 대한 진입차수는 0으로 초기화
indegree = [0] * (v + 1)
# 각 노드에 연결된 간선 정보를 담기 위한 연결 리스트 초기화
graph = [[] for _ in range(v + 1)]

# 방향 그래프의 모든 간선 정보를 입력받기
for _ in range(e):
    a, b = map(int, sys.stdin.readline().rstrip().split())
    graph[a].append(b)
    indegree[b] += 1   # b로 들어오는 indegree 개수 + 1

# 위상 정렬 함수
def topology_sort():
    result = []
    q = deque()

    # 처음에는 진입차수가 0 인 노드를 큐에 삽입
    for i in range(1, v + 1):
        if indegree[i] == 0:
            q.append(i)

    # 큐가 빌 때까지 반복
    while q:
        # 큐에서 원소 꺼내기
        now = q.popleft()
        result.append(now)
        print(now, graph[now])
        # 해당 원소와 연결된 노드들의 진입차수에서 1 빼기
        for i in graph[now]:
            indegree[i] -= 1
            # 새롭게 진입차수가 0이 되는 노드를 큐에 삽입
            if indegree[i] == 0:
                q.append(i)

    for i in result:
        print(i, end=' ')

topology_sort()
```

위상 정렬의 시간 복잡도는 O(V + E)이다. 차례대로 모든 노드를 확인하면서 해당 노드에서 출발하는 간선을 차례때로 제거하기 때문이다.