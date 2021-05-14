---
title: \[알고리즘] 크루스칼(Kruskal)
categories: 
  - algorithms
tags: 
  - graph
  - kruskal
toc: true
---

### 크루스칼(Kruskal) 알고리즘이란?

---

크루스칼 알고리즘이란 MST(최소 신장 트리)를 찾는 알고리즘이다.

- 모든 간선 데이터를 가중치에 따라 오름차순으로 정렬
- 간선을 하나씩 확인하며 사이클 발생여부를 판별 → find연산으로 같은 집합에 속하는지 확인
- 사이클이 발생하지 않는다 → MST집합에 포함 / 발생한다 → 포함 X
- 모든 간선에 대해 2, 3번을 반복한다.

위 알고리즘을 보면 알 수 있겠지만 간선 데이터를 오름차순으로 정렬하고 이를 순차적으로 뽑아 최적의 해를 도출할 수 있기 때문에 그리디 알고리즘으로 분류된다.

시간복잡도는 모든 간선을 오름차순으로 정렬하는 것이 O(ElogE)로 가장 오래걸린다. union-find를 하는 과정은 이보다 시간복잡도가 적기 때문에 크루스칼 알고리즘의 시간복잡도는 O(ElogE)이다.

### 구현(Python)

---

```python
def find(x, parent[x]):
    if x != parent[x]:
        parent[x] = find_parent(parent[x], parent)
    return parent[x]

def union(a, b, parent):
    a = find(a, parent)
    b = find(b, parent)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

def solution(v, edges):    # edges = [cost, a, b]
    parent = [0] * (v + 1)
    for i in range(v):
        parent[i] = i

    edges.sort()    # 오름차순 정렬
    
    answer = 0
    for cost, a, b in edges:
        # 사이클이 발생하지 않는 경우에 대해서만 MST에 포함
        if find(a, parent) == find(b, parent):
            union(a, b, parent)
            answer += cost
    return answer
```