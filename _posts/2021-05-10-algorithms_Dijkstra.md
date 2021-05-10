---
title: \[알고리즘] 다익스트라(Dijkstra)
categories: 
  - algorithms
tags: 
  - shortest path
  - dijkstra
toc: true
---

### 다익스트라 알고리즘이란?

---

다익스트라 최단 경로 알고리즘은 그래프에서 여러 개의 노드가 있을 때, 특정한 노드(Single Source)에서 출발하여 다른 노드로 가는 각각(All Destination)의 최단 경로를 구해주는 알고리즘이다.

다익스트라 알고리즘은 음의 간선을 포함하면 사용할 수 없다는 특징을 갖고 있다.

앞서 살펴 보았던 Optimal Substructure의 원리(최단 거리는, 여러 개의 최단 거리로 이루어져 있음)에 의해, 다익스트라 알고리즘은, 매 순간 시작점으로부터 최단 거리의 노드로 이동하여 수행한다는 점이 그리디 하다고 볼 수 있다. 다익스트라는 하나의 최단거리를 구할 때 그 이전까지 구했던 최단 거리 정보를 그대로 사용한다는 특징으로부터 비롯해 다이나믹 프로그래밍을 활용한다고 볼 수도 있다.

### 어떻게 동작하는가?

---

다익스트라 알고리즘 동작원리를 알아보자. 다음과 같은 그래프가 존재하고, 시작점을 1이라고 해보자.

우선 distance 배열은 1에서부터의 최단 거리를 의미하고, hq는 우선순위큐를 의미한다. 우선순위큐를 이용하는 이유는 동작 원리 도중 자세하게 다룬다.

![dijkstra1.jpeg](/assets/images/algorithms/dijkstra1.jpeg)

초기에 distance 배열은 시작점은 0으로, 나머지는 모두 무한대(int(1e9) 등)로 초기화 한다.

이후, 1과 인접한(연결된 간선이 있는) 노드들의 정보를 우선순위큐에 [거리, 노드번호] 형태의 튜플로 저장한다. 여기서 거리는 우선순위큐의 key가 된다. 즉 Min-Heap 자료구조를 사용하는 것과 동일하다. 이후는 minheap으로 표기한다. 자세한 내용은 뒤에서 설명한다.

min-heap 자료구조에 하나씩 넣으면서 distance 배열도 위 사진처럼 같이 업데이트 한다. 이 의미는, 1에서부터 2, 3, 4, 5까지의 최단 거리를 의미한다. 아직 5 까지는 도달할 수 없으므로 최단경로가 INF로 유지된다.

![dijkstra2.jpeg](/assets/images/algorithms/dijkstra2.jpeg)

min-heap에서 Top 노드를 빼서 해당 노드로 이동한다. 이전 min-heap에서 top 노드는 2였기에 2번 노드로 이동하게 된다.

여기서, 왜 min-heap을 사용하게 되었는지를 알 수 있다. 

1 기준에서 가장 가까운 노드를 고르면, **다른 어떤 노드를 거쳐서 해당 노드로 가도 바로 가는 것 보다 짧을 수 없다.** 따라서 1에서 출발하여 2로 가는 최단 경로는  1 → 2 가 되는 것이고 2는 더이상 방문 할 필요가 없게 된다. 이 원리가 음수 간선이 있다면 해당되지 않으므로, 다익스트라는 음수 간선이 존재할 때 사용할 수 없게 되는 것이다.

![dijkstra3.jpeg](/assets/images/algorithms/dijkstra3.jpeg)

이 다음엔 min-heap에서 pop하고 3의 모든 인접 노드들을 탐색한다. 여기서 1은 이미 방문했던 노드이므로 재방문 할 필요가 없고, 4, 5 와 연결된 노드들을 탐색한다. 이때 4, 5까지의 거리는 이미 1 → 3 까지의 최단 경로에서 업데이트를 해야 한다. 따라서 cost = dist + adj[0] 이라는 식이 완성된다. 새로운 4, 5 까지의 거리는 3 까지의 거리 + 3과 4, 5와 연결된 거리 라는 의미이다.

이를 구한 후 min-heap에 push하고, distance 배열도 위와 같이 업데이트 한다.

이를 통해 알 수 있는 사실은, 다익스트라 알고리즘은 **현재 노드를 경유하였을 때 더욱 짧은 경로가 되는지를 검사하는 알고리즘이다.**

![dijkstra4.jpeg](/assets/images/algorithms/dijkstra4.jpeg)

다음 top 노드인 4를 pop 하고 cost를 구해 업데이트 해본다. 1 → 3 → 4 → 5 의 cost 는 14이지만, 이미 1 → 3 → 5 까지의 cost인 6이 있으므로 업데이트 되지 않는다.

위 원리에서 사실 필요 없는 부분이 있다. cost가 만약 기존보다 더 크다면 굳이 min-heap에 push할 이유가 없다. 따라서 해당 세부 사항까지 고려하여 코드를 작성하면 아래와 같다.

### 구현(Python) - using Min-heap

---

```python
def dijkstra(start):
    hq = []
    # 시작 노드로 가기 위한 최단 경로는 0으로 설정하여, 힙에 삽입
    heapq.heappush(hq, (0, start))
    distance[start] = 0
    while hq:  # 힙이 비어있지 않다면
        # 가장 최단 거리가 짧은 노드에 대한 정보 꺼내기
        dist, now = heapq.heappop(hq)
        # 현재 노드가 이미 처리된 적이 있는 노드라면 무시
        # 이미 처리한 적이 있는 노드라는 뜻은, 이미 최단경로가 구해진 노드라는 뜻이다.
        if distance[now] < dist:
            continue
        # 현재 노드와 연결된 다른 인접한 노드들을 확인
        for adj in graph[now]:     # adj -> (목적지(des), 거리(weight))
            cost = dist + adj[1]
            # 현재 노드를 거쳐서, 다른 노드로 이동하는 거리가 더 짧은 경우
            if cost < distance[adj[0]]:
                distance[adj[0]] = cost
                heapq.heappush(q, (cost, adj[0]))
```