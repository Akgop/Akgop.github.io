---
title: \[BOJ] 19236 청소년 상어
categories: 
  - BOJ
tags: 
  - backtracking
  - simulation
toc: true
---

### 백트래킹, 시뮬레이션

---

해당 문제는 판이 4x4로 고정되어 있고, 정말 복잡한 전제 조건들이 주어진다. 따라서 문제를 해결할때 전제 조건을 하나씩 고려해가며 제대로 이해했는지 검증하며 차근차근 풀어나가는게 핵심 포인트이다.

우선 나는 다음과 같이 생각해봤다.

- 여러 기능을 구현해야 한다. → 기능에는 크게 물고기 이동, 상어 이동으로 이루어져있다.
- 물고기 이동
    - 물고기는 방향에 따라 움직이고 방향은 반시계 반대 방향으로 총 8방향으로 이루어진다. 즉, 결국 한바퀴 돌며 어떻게든 모든 물고기는 1회 이동할 것이다.
    - 따라서 구현해야할 기능은 회전(rotate), 이동(move)이다.
- 상어 이동
    - 상어는 0, 0 물고기 위치를 잡아먹으며 시작하고, 그 물고기가 바라보던 방향으로 이동할 수 있고, 해당 방향에 있는 물고기중 하나를 골라 먹을 수 있다.
    - 따라서 구현해야 할 기능은 3가지를 전부 시도해보는 백트래킹(DFS) 기능이다. 사실 완전 탐색으로 볼 수도 있지만, 상어가 더 이상 움직이지 않는다면 반환하는 백트래킹으로 보는게 맞다고 판단했다.

위 기능을 하나씩 제대로 구현했는지 검증해나가며 풀어야 하므로 회전 → 물고기 이동 → 상어 이동 순서로 구현했다. 코드는 다음과 같다.

### 구현

---

상어는 임의로 17로 두고 풀었고, 빈 공간은 0으로 두고 풀었다.

```python
import sys
import copy

# 인덱스는 1부터
dx = [0, -1, -1, 0, 1, 1, 1, 0, -1]
dy = [0, 0, -1, -1, -1, 0, 1, 1, 1]

def rotate(graph, direc, i, j, n):
    ni, nj = 0, 0
    for r in range(8):
        ni = i + dx[direc[i][j]]
        nj = j + dy[direc[i][j]]
        # 스왑 하려는 위치가 벽이거나 상어임 -> 회전
        if ni < 0 or nj < 0 or ni >= n or nj >= n or graph[ni][nj] == 17:
            direc[i][j] += 1
            if direc[i][j] > 8:
                direc[i][j] = 1
        # 스왑 가능한 위치.
        else:
            break
    return ni, nj, direc

def move_fish(graph, direc, target, n):
    for i in range(n):
        for j in range(n):
            # 이동해야 하는 물고기 위치 찾음.
            if graph[i][j] == target:
                # 스왑하려는 위치가 벽이거나 상어일 수도 있으니 체크 & 회전
                ni, nj, direc = rotate(graph, direc, i, j, n)
                # 이동(물고기 위치 스왑)
                graph[i][j], graph[ni][nj] = graph[ni][nj], graph[i][j]
                direc[i][j], direc[ni][nj] = direc[ni][nj], direc[i][j]
                return graph, direc
    return graph, direc

def move_all_fish(graph, direc, n):
    for f in range(1, 17):
        graph, direc = move_fish(graph, direc, f, n)
    return graph, direc

def move_shark(graph, direc, px, py, x, y, n):
    # 만약 이동한 위치가 인덱스 범위 밖이거나, 빈 공간일 경우 상어는 더이상 먹이를 먹을 수 없음
    if x < 0 or y < 0 or x >= n or y >= n or graph[x][y] == 0:
        return 0

    # 이전 상어의 위치를 빈 공간으로 만들어줌
    graph[px][py] = 0
    direc[px][py] = 0
    # 상어가 현재 위치의 물고기를 잡아먹음, 물고기 이동
    current_fish = graph[x][y]
    graph[x][y] = 17
    graph, direc = move_all_fish(graph, direc, n)

    nx, ny = dx[direc[x][y]], dy[direc[x][y]]
    # 다음 3가지의 sub problem 을 구함
    tmp_graph, tmp_direc = copy.deepcopy(graph), copy.deepcopy(direc)
    first = move_shark(graph, direc, x, y, x + nx, y + ny, n)
    graph, direc = copy.deepcopy(tmp_graph), copy.deepcopy(tmp_direc)

    second = move_shark(graph, direc, x, y, x + nx * 2, y + ny * 2, n)
    graph, direc = copy.deepcopy(tmp_graph), copy.deepcopy(tmp_direc)

    third = move_shark(graph, direc, x, y, x + nx * 3, y + ny * 3, n)

    return current_fish + max(first, second, third)

def solution():
    # 초기 세팅
    direc, graph, n = [], [], 4
    for _ in range(n):
        tmp = list(map(int, sys.stdin.readline().rstrip().split()))
        graph.append([tmp[0], tmp[2], tmp[4], tmp[6]])
        direc.append([tmp[1], tmp[3], tmp[5], tmp[7]])

    # 초기 상어 세팅, 물고기 이동
    first_fish = graph[0][0]
    x, y = 0, 0
    graph[x][y] = 17
    graph, direc = move_all_fish(graph, direc, n)

    nx, ny = dx[direc[x][y]], dy[direc[x][y]]

    # 상어 이동의 3가지 sub problem 의 결과를 받는다.
    tmp_graph, tmp_direc = copy.deepcopy(graph), copy.deepcopy(direc)
    first = move_shark(graph, direc, x, y, x + nx, y + ny, n)
    graph, direc = copy.deepcopy(tmp_graph), copy.deepcopy(tmp_direc)

    second = move_shark(graph, direc, x, y, x + nx*2, y + ny*2, n)
    graph, direc = copy.deepcopy(tmp_graph), copy.deepcopy(tmp_direc)

    third = move_shark(graph, direc, x, y, x + nx*3, y + ny*3, n)

    return first_fish + max(first, second, third)

if __name__ == "__main__":
    print(solution())
```