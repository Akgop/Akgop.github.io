---
title: \[알고리즘] 최소신장트리(MST, Minimum Spanning Tree)
categories: 
  - algorithms
tags: 
  - graph
  - minimum spanning tree
toc: true
---

### 신장 트리(Spanning Tree)란?

---

신장 트리란 하나의 그래프에 대해 다음 조건을 만족해야한다.

- 모든 노드 포함, 모두 서로 연결
- 사이클이 존재하지 않는 부분 그래프 = 트리의 성립 조건

### 최소 신장 트리(Mimum Spanning Tree)란?

---

가능한 모든 신장 트리중, 최소한의 비용을 갖는 신장트리. 즉, 이어진 간선의 합이 최소가 되는 신장트리.

### 최소 신장 트리를 구하는 알고리즘

---

[크루스칼(Kruskal)](https://akgop.github.io/algorithms/algorithms_Kruskal)

번외: 왜 span인가? → span의 뜻 중, 두 곳을 걸친다, 뻗어나간다 라는 의미가 있다. 따라서 spanning tree라고 할 때 모든 노드를 걸친 트리 정도로 이해하면 될듯 싶다.