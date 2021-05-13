---
title: \[알고리즘] Union-Find
categories: 
  - algorithms
tags: 
  - shortest path
  - floyd
toc: true
---

### Union-Find 알고리즘이란?

---

서로소 집합(Disjoint Sets)이란?

- 서로 공통 원소가 없는 두 집합을 의미한다.
- 즉, 두 그래프가 연결되어 있지 않은 상태를 말한다.

서로소 집합 자료구조란?

- 서로소 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조
- 해당 자료구조를 union, find 연산 2개로 조작하기 때문에 union-find 자료구조라고 불리기도 한다.

서로소 집합 자료구조를 구현하기 위해서는 트리 자료구조를 이용하여 집합을 표현한다.

- union 연산을 통해 A, B 두 노드를 합친다. 합친다는 말은 A 노드와 B 노드를 서로 연결한다는 뜻이고, 이는 트리 구조를 통해 표현된다.
- 모든 edge에 대해 union 연산을 처리할 때까지 위 과정을 반복한다.
- 즉, 모든 연결 요소를 확인하여 트리 자료구조로 구성하는 것이다!
- O ( V + M ( 1 + log V ) ) < O( V M ) → 노드의 개수가 1000개, union + find 연산이 총 100만 번 가량 수행된다고 할 때 1000만회의 연산이 필요하다고 기억해놓자. (코딩테스트시 입력의 경우에 따라 알고리즘 사용 여부 판별하기 위해서)

### 동작 원리

---

예시를 들어보자. 다음과 같은 그래프가 있다고 해보자 

![unionfind1.jpeg](/assets/images/algorithms/unionfind1.jpeg)

우리는 한눈에 보고 A,B,C,D 와 E,F 가 다른 그룹이라는 걸 알 수 있지만 컴퓨터는 모른다. 따라서 union find 알고리즘을 사용한다. 우선 간선은 a, b, c, d 순으로 연산한다고 가정하자.

우선 모든 연산을 하기 전에 parent라는 배열을 선언한다. parent배열은 자신이 포함된 집합(트리구조)의 꼭대기 root노드 값을 지정하고 있는 배열이다. 그리고 자기 자신 노드를 root노드로 지정한다. 그래야 모든 노드가 각각 다른 집합에 속하는 상태가 되는 것이다. {A}, {B}, {C}, {D}, {E}, {F} 로 구분한다고 생각하면 된다.

이후 위 그림처럼 A, B 두 노드에 대해 union을 수행하면 {A, B}, {C}, {D}, {E}, {F} 가 된다. B의 root가 A가 되는 것이다.

![unionfind2.jpeg](/assets/images/algorithms/unionfind2.jpeg)

이후 b 간선에 대해 양쪽 노드를 union을 수행한다. union 연산에는 두 노드에 대해 root노드를 찾아서 두 root노드를 union하는 과정이기 때문에 내부적으로 파란색으로 표시한 대로 동작한다. 따라서 C 노드도 A노드를 root노드로 갖게된다. 이후 D, E, F 모두 같은 방식으로 수행된다. 코드가 간단하니 코드를 보면 이해가 쉽다.

### 구현 (Python) - union-find

---

```python
def find(x, parent):
    # 현재 노드가 루트가 아니라면
    if x != parent[x]:
        # 루트 노드를 찾아서 저장(재귀적으로 부모를 타고 올라감)
        parent[x] = find(parent[x], parent)
    # x의 루트 노드 반환
    return parent[x]

def union(a, b, parent):
    a = find(a, parent)
    b = find(b, parent)
    # 두 root를 합칠 때 노드 번호가 작은 노드를 root로 설정하기 위한 일종의 기법
    # 더 큰 루트 노드가 더 작은 루트 노드를 가리키도록 하는 방법
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# edges는 연결된 간선 표현, n은 노드의 개수라고 하자
def union_find(edges, n):
    parent = [0] * (n + 1)   # 인덱스가 1부터 시작할 경우 n + 1로 하면 편하다
    for a, b in edges:
        union(a, b, parent)
```

### 사이클 판별(cycle detection)

---

어떤 그래프가 cycle을 이루는지 알아보기 위해서는 2가지 방법을 사용할 수 있다.

- 방향 그래프: DFS
- 무방향 그래프: union-find

본 포스트에서는 무방향 그래프에서 union-find를 통한 사이클 판별을 알아보고자 한다.

원리는 다음과 같다

- 루트 노드가 서로 다르다면? → union연산을 수행
- 루트 노드가 서로 같다면? → cycle 발생!

왜 루트 노드가 같을 때 cycle이 발생하는지는 매우 간단한 원리이다. N개의 노드가 있을 때 모두 같은 그룹내에 속해있고 사이클이 발생하지 않으려면 간선의 개수는 N-1개로 고정되어야 한다. 즉, 새로운 노드가 추가될 때 이미 같은 그룹 내에 있는 노드끼리의 추가라면 노드의 개수는 N 개인데, 간선의 개수가 N 개가 되어버려, 이미 같은 그룹 내의 노드끼리 간선이 추가되어 사이클이 발생하게 된다.

![unionfind3.jpeg](/assets/images/algorithms/unionfind3.jpeg)

### 구현(Python) - cycle detection

---

```python
def find(x, parent):
    if x != parent[x]:
        parent[x] = find(parent[x], parent)
    return parent[x]

def union(a, b, parent):
    a = find(a, parent)
    b = find(b, parent)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

def cycle_detection(v, e):
    # 부모 테이블에서 루트 노드를 자기 자신으로 초기화
    parent = [0] * (v + 1)
    for i in range(1, v + 1):
        parent[i] = i

    # 사이클 발생 여부 flag
    cycle = False

    for i in range(e):    # 모든 간선 순회하며
        a, b = map(int, input().split())
        # 사이클이 여부 판별 -> 발생시 종료
        if find_parent(parent, a) == find_parent(parent, b):
            cycle = True
            break
        else:       # 발생하지 않았다면 union 연산 수행
            union_parent(parent, a, b)
```