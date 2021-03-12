---
title: \[이코테] 8. 최단 경로
categories: 
  - thisiscote
tags: 
  - shortest path
toc: true
---

### 관련 코드 구현

---

[[BOJ] 1504 특정한 최단경로](http://akgop.github.io/boj/BOJ1504/)<br>
[[BOJ] 1753 최단경로](http://akgop.github.io/boj/BOJ1753/)<br>
[[BOJ] 11404 플로이드](http://akgop.github.io/boj/BOJ11404/)<br>
[[이코테] 실전 문제(예제)](http://akgop.github.io/thisiscote/thisiscote_81/)

### 최단 경로(Shortest Path)

---

최단 경로 알고리즘은 가장 짧은 경로를 찾는 알고리즘이다.

최단 경로 알고리즘은 보통 그래프로 표현하는데 각 지점은 그래프에서 노드(Node, Vertex)로 표현되고, 각 지점 간 연결된 도로는 그래프에서 간선(Edge)로 표현된다. 해당 챕터에서 크게 2가지 알고리즘을 다룬다.

- 다익스트라(Dijkstra): 그리디 알고리즘, Single Source - All Destination, O(ElogV)
- 플로이드-워셜(Floyd-Warshall): 다이나믹 프로그래밍, All Source - All Destination, O(N^3)

### 다익스트라(Dijkstra) 최단 경로 알고리즘

---

다익스트라는 특정한 노드 하나에서 출발하여 다른 연결된 모든 노드로 가는 각각의 최단 경로를 구해주는 알고리즘이다. 음의 간선(Negative edge)가 없을 때 정상적으로 동작한다.

다익스트라는 기본적으로 그리디이다. 매번 가장 비용이 적은 노드를 선택해서 임의의 과정을 반복하기 때문이다.

1. 출발 노드 설정
2. 최단 거리 테이블(distance list)초기화
3. 방문하지 않은 노드 중, 최단 거리가 가장 짧은 노드 선택
4. 해당 노드를 거쳐 다른 노드로 가는 비용을 계산하여 최단 거리 테이블 갱신
5. 3, 4번 반복

다익스트라는 방문하지 않은 노드 중에서 가장 최단 거리가 짧은 노드를 선택하는 과정을 반복하는데(3번 과정), 이렇게 선택된 노드는 이미 최단 거리가 결정난 노드이므로 더이상 반복해도 최단 거리가 줄어들지 않는다. 즉, 한 단계당 하나의 노드에 대한 최단거리를 확실히 찾는 것으로 이해할 수 있다. 따라서 마지막 노드는 다른 노드에 대해 더 짧은 경우가 있는지 볼 필요가 없다.

다익스트라를 구현하는 방법은 3번을 어떻게 구현하냐에 따라서 구현 난이도와 성능에서 차이가 난다.

아래 코드는 쉽게 구현할 수 있는 대신, O(V^2)의 성능을 갖는다.

```python
import sys

INF = int(1e9)

# 노드의 개수, 간선의 개수를 입력받기
node, edge = map(int, sys.stdin.readline().rstrip().split())

# 시작 노드 입력받기
start = int(sys.stdin.readline().rstrip())

# 그래프 생성, 방문 노드 체크 리스트 생성, 최단거리 테이블 생성 및 무한으로 초기화
graph = [[] for _ in range(node + 1)]
visited = [False] * (node + 1)
distance = [INF] * (node + 1)

# 모든 간선 정보 입력받기
for _ in range(edge):
    src, des, weight = map(int, sys.stdin.readline().rstrip().split())
    graph[src].append((des, weight))

def get_smallest_node():   # O(V)
    min_value = INF
    index = 0
    for i in range(1, node + 1):
        if distance[i] < min_value and not visited[i]:
            min_value = distance[i]
            index = i
    return index

def dijkstra(start):
    # 시작 노드 초기화
    distance[start] = 0
    visited[start] = True
    for j in graph[start]:
        distance[j[0]] = j[1]
    # 시작 노드를 제외한 전체 n-1 개의 노드에 대해 반복
    for i in range(node-1):
        # 현재 최단 거리가 가장 짧은 노드를 꺼내서, 방문 처리
        now = get_smallest_node()
        visited[now] = True
        # 현재 노드와 연결된 다른 노드를 확인
        for adj_node in graph[now]:
            cost = distance[now] + adj_node[1]
            # 기존 값보다 더 짧은 경우
            if cost < distance[adj_node[0]]:
                distance[adj_node[0]] = cost

dijkstra(start)

# 결과 출력
for i in range(1, node + 1):
    if distance[i] == INF:
        print("무한")
    else:
        print(distance[i])
```

위 코드는 최단 거리가 가장 짧은 노드를 매번 순차탐색 하기 때문에 O(V)가 소요된다. 따라서 코딩 테스트 문제에서 전체 노드의 개수가 5000개 이하라면 위 코드를 사용해도 시간에 구애받지 않는다. 하지만 10000개가 넘어가는 문제라면 시간초과가 발생할 수 있다.

따라서 get_smallest_node의 순차 탐색이 아닌, 최소 힙을 이용해 구현한다.

힙과 우선순위 큐에 대한 설명은 아래 링크에 자세하게 설명했다.

[힙, 우선순위 큐, heapq](https://akgop.github.io/data_structure/datastructure_priorityqueue/)

개선된 다익스트라 알고리즘 코드는 아래와 같다.

```python
"""
다익스트라 알고리즘 - 최소 힙을 사용한
O(E*logV)
Single Source - All Destination
"""
import heapq
import sys

INF = int(1e9)

# 노드의 개수, 간선의 개수를 입력받기
node, edge = map(int, sys.stdin.readline().rstrip().split())

# 시작 노드 번호 입력받기
start = int(sys.stdin.readline().rstrip())

# 그래프 생성하고, 결과 리스트는 모두 무한대로 초기화
graph = [[] for i in range(node + 1)]
distance = [INF] * (node + 1)

# 모든 간선(edge)정보 입력받기
for _ in range(edge):
    src, des, weight = map(int, sys.stdin.readline().rstrip().split())
    graph[src].append((des, weight))

def dijkstra(start):
    q = []
    # 시작 노드로 가기 위한 최단 경로는 0으로 설정, 큐에 삽입
    heapq.heappush(q, (0, start))
    distance[start] = 0
    while q:        # 큐가 비어있지 않다면
        # 가장 최단 거리가 짧은 노드에 대한 정보 꺼내기
        dist, now = heapq.heappop(q)
        # 현재 노드가 이미 처리된 적이 있는 노드라면 무시
        if distance[now] < dist:
            continue
        # 현재 노드와 연결된 다른 인접한 노드들을 확인
        for adj_node in graph[now]:     # adj_node -> (목적지(des), 거리(weight))
            cost = dist + adj_node[1]
            # 현재 노드를 거쳐서, 다른 노드로 이동하는 거리가 더 짧은 경우
            if cost < distance[adj_node[0]]:
                distance[adj_node[0]] = cost
                heapq.heappush(q, (cost, adj_node[0]))

# 다익스트라 수행
dijkstra(start)

# 모든 노드로 가기 위한 최단 거리 출력
for i in range(1, node + 1):
    # 도달할 수 없는 경우 무한 출력
    if distance[i] == INF:
        print("무한")
    else:
        print(distance[i])
```

다익스트라는 우선순위 큐를 필요로 하는 다른 문제 유형과도 흡사하다. 그래프 문제로 유명한 최소 신장 트리(MST, Minimum Spanning Tree)를 풀 때에도 Prim 알고리즘의 구현이 다익스트라 알고리즘의 구현과 흡사하다는 특징이 있다.

### 플로이드-워셜(Floyd-Warshall) 알고리즘

---

플로이드 워셜 알고리즘은 모든 지점에서 다른 모든 지점까지의 최단 경로를 구할 때 유용한 알고리즘이다.

노드의 개수가 N개라고 하자. 모든 지점에서 모든 지점으로의 최단 거리를 구하기 때문에 2차원 리스트를 사용한다. 따라서 모든 거리를 한 번 탐색하기 위해선 O(N^2)만큼 소요된다. 이를 노드의 개수만큼 반복해야 하므로 총 O(N^3)만큼 소요된다.

플로이드 워셜 알고리즘은 다이나믹 프로그래밍이다. 아이디어는 간단하다. 현재 노드가 K라고 할 때 A → B와 A → K → B 를 비교하여 더 거리가 짧은 노드로 업데이트 하는 원리이다. 

- Dab = min(Dab, Dak + Dkb)

따라서 3중 포문을 이용하여 코드를 아래와 같이 구현한다.

```python
"""
플로이드 워셜 알고리즘
All Source - All Destination
시간 복잡도: O(N^3)
"""

import sys

INF = int(1e9)

# 노드의 개수 및 간선의 개수를 입력받기
node = int(sys.stdin.readline().rstrip())
edge = int(sys.stdin.readline().rstrip())

# 2차원 리스트(그래프) 생성, 무한으로 초기화
graph = [[INF] * (node + 1) for _ in range(node + 1)]  # 인덱스 1부터 라고 가정해서 node + 1

# 자기 자신으로 가는 비용은 0으로 초기화 (대각 행렬 0으로 초기화)
for i in range(1, node + 1):
    for j in range(1, node + 1):
        if i == j:
            graph[i][j] = 0

# 주어진 입력 받아서 초기화
for _ in range(edge):
    src, des, weight = map(int, sys.stdin.readline().rstrip().split())
    graph[src][des] = weight
    # 양방향일 경우 아래 한 줄 추가
    # graph[des][src] = weight

# 점화식에 따라 floyd-warshall 알고리즘 수행
for k in range(1, node + 1):
    for i in range(1, node + 1):
        for j in range(1, node + 1):
            # k를 지나치는 경로와, 직접 가는 경로중 더 짧은 경로로 업데이트
            graph[i][j] = min(graph[i][j], graph[i][k], graph[k][j])

# 결과 출력
for i in range(1, node + 1):
    for j in range(1, node + 1):
        # 도달할 수 없는 경우
        if graph[i][j] == INF:
            print("무한", end=' ')
        else:
            print(graph[i][j], end=' ')
    print()
```
