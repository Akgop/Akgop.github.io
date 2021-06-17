---
title: \[BOJ] 16236 아기상어
categories: 
  - BOJ
tags: 
  - bfs
  - simulation
toc: true
---
### BFS

---

해당 문제는 BFS + 시뮬레이션 유형이다. 가까운 거리의 먹이부터 먹는다 라는 점에서 BFS임을 확인할 수 있다. 그리고 가까운 거리란 움직이는 이동거리라고 했으므로 L1 distance가 된다. 따라서 고려해야할 조건은 다음과 같다.

- 먹을 수 있는 물고기가 있는지?
- 먹을 수 있는 물고기들 중 무엇을 먹을 것 인지? 우선순위는 위 → 왼쪽 순서이다.

여기서 우선순위를 고려할때 막연하게 BFS의 움직임을 위→왼→오→아래 로 한다면 틀린 답이 나올 것이다. 따라서 같은 레벨에 존재하는 먹이가 될 수 있는 물고기들에 대한 자료구조를 정의해서 우선순위가 가장 높은 곳으로 이동해야 한다.

우선순위는 heap 자료구조를 사용하여 우선순위큐를 통해 구현했다. 거리 → x(상하) → y(좌우) 좌표 순으로 key값으로 사용하도록 했다. 따라서 같은 거리에서 가장 위쪽에 있는 물고기를, 그 다음엔 가장 왼쪽에 있는 물고기를 먹을 수 있도록 하면 된다. 코드는 아래와 같다.

### 구현

---

```python
import sys
from collections import deque
import heapq

dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]

def bfs(start, n, size):
    heap = []
    q = deque()
    q.append((start[0], start[1], 0))
    visited = [[False] * n for _ in range(n)]
    visited[start[0]][start[1]] = True
    dist = 0
    while q:
        x, y, dist = q.popleft()
        if heap:
            if heap[0][0] < dist:
                dist, x, y = heapq.heappop(heap)
                graph[x][y] = 0
                return x, y, dist
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            # 인덱스 범위 밖
            if nx < 0 or ny < 0 or nx >= n or ny >= n:
                continue
            # 벽(숫자가 큰 물고기만남)
            if graph[nx][ny] > size:
                continue
            # 이미 방문한 공간
            if visited[nx][ny]:
                continue
            # 더 작은 물고기를 만남 -> 먹잇감으로 지정
            if 0 < graph[nx][ny] < size:
                heapq.heappush(heap, (dist + 1, nx, ny))
            # 그냥 이동 경로임
            q.append((nx, ny, dist+1))
            visited[nx][ny] = True
    if heap:
        dist, x, y = heapq.heappop(heap)
        graph[x][y] = 0
        return x, y, dist
    return -1, -1, 0

def solution(n):
    # 초기 변수들 설정
    size = 2
    count = 0
    time = 0

    # 상어 위치 찾음
    start_x, start_y = 0, 0
    for i in range(n):
        for j in range(n):
            if graph[i][j] == 9:
                start_x, start_y = i, j

    # 상어가 엄마를 찾을 때 까지 반복
    while True:
        next_x, next_y, spent_time = bfs((start_x, start_y), n, size)
        graph[start_x][start_y] = 0
        # 엄마 상어 찾음
        if next_x == -1 and next_y == -1:
            break
        start_x, start_y = next_x, next_y
        time += spent_time
        count += 1      # 먹은 횟수 + 1
        if count == size:   # 상어의 크기 + 1
            count = 0
            size += 1
    return time

if __name__ == "__main__":
    N = int(sys.stdin.readline().rstrip())
    graph = []
    for _ in range(N):
        graph.append(list(map(int, sys.stdin.readline().rstrip().split())))
    print(solution(N))

```