---
title: \[프로그래머스] 3주차 퍼즐 조각 채우기
categories: 
  - programmers
tags:
  - bfs
  - simulation
  - bruteforce
toc: true
---

### BFS, Simulation, Brute Force

---

이 문제는 테이블 위에 조각을 맞추는 문제이다. 그리고 최대값을 구하는 문제이다.

입력이 0,1 꼴 2차원 배열로 주어진다. 따라서 조각을 어떻게 추출해서 빈 공간에 대입해 볼 것인가에 대한 아이디어가 어려운 문제이다.

따라서 우리는 근본에 집중해야 한다. 그래프에서 조각을 "탐색" 해야 하므로 DFS, BFS를 떠올려야 한다. 여기서 엿(?) 모양의 블록과 같이 DFS를 이용하면 조금 까다로운 유형이 있어서.. BFS로 모든 노드들을 탐색하기로 했다.

그러면 이 탐색한 블록을 어떻게 저장할 것인가에 대해 고민해봐야한다. 이를 좌표로 저장하면 보드에 매칭해보기 너무 어렵다고 생각했다. 따라서 x, y 좌표의 최소 최대값을 저장해서 직사각형 모양으로 추출했다.

그리고 조각의 크기를 `count`라는 변수로 세어줬다. 이유는, 어차피 조각의 크기가 다르면 비교할 필요도 없기 때문이다. (비교 연산을 최대한 줄이기 위해, 물론 워스트 케이스는 비교를 전부 같은 크기의 조각들만 등장하는 경우이다.)

위 과정을 `game_board`, `table` 에 모두 진행했다. `game_board`에서는 0을 추출해야 하고 `table`에서는 1을 추출해야 하기 때문에 XOR 연산을 통해 table을 반전시켰다.

그리고 90도 회전하는 함수와 `check`함수를 구현하여 size 별로 서로 비교하는 함수를 구현 하여 올 케이스에 대해 커버하는 코드를 구현했다. (브루트포스)

### 알고리즘

---

1. `game_board`, `table`에 대해 조각을 구함. (서로 비교하기 위함)
    1. BFS 사용, 직사각형으로 추출
2. 사이즈별로 분류된 조각을 서로 비교하여 맞춰봄
    1. 만약 정확히 조각 모양이 일치한다 → `answer += size`
    2. 조각이 일치하지 않는다면 `rotate`
    3. 사용한 조각은 `used`로 마킹하여 재사용하지 못하게 한다.

### 구현

---

알고리즘이 어려운 문제는 아닌데, 구현 능력이 매우 필요한 문제이다. 이걸 시간내에 풀기는 꽤 난이도 있어 보인다.

```python
from collections import deque

dx = [-1, 1, 0, 0]
dy = [0, 0, -1, 1]

def bfs(start, board, visited):
    que = deque()
    que.append(start)
    visited[start[0]][start[1]] = True
    n = len(board)
    min_x, min_y, max_x, max_y = n, n, -1, -1
    count = 0
    while que:
        x, y = que.popleft()
        count += 1
        if x < min_x:
            min_x = x
        if y < min_y:
            min_y = y
        if x > max_x:
            max_x = x
        if y > max_y:
            max_y = y
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or ny < 0 or nx >= n or ny >= n:
                continue
            if visited[nx][ny]:
                continue
            if board[nx][ny] == 0:
                visited[nx][ny] = True
                que.append((nx, ny))

    piece = [[] for _ in range(max_x+1-min_x)]
    idx = 0
    for i in range(min_x, max_x+1):
        for j in range(min_y, max_y+1):
            piece[idx].append(board[i][j])
        idx += 1
    return count, piece

def get_pieces(board):
    result = [[] for _ in range(7)]
    n = len(board)
    visited = [[False] * n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if board[i][j] == 0 and not visited[i][j]:
                count, piece = bfs((i, j), board, visited)
                result[count].append(piece)
    return result

def turn_right(a):
    row_length = len(a)
    column_length = len(a[0])
    result = [[0] * row_length for _ in range(column_length)]
    for r in range(row_length):
        for c in range(column_length):
            result[c][row_length - 1 - r] = a[r][c]
    return result

def check(board, table, size):
    result = 0
    if not board or not table:
        return result

    flag = False
    used = [False] * len(table)
    for i in range(len(board)):
        for j in range(len(table)):
            if used[j]:
                continue
            for r in range(4):
                if board[i] == table[j]:
                    used[j] = True
                    result += size
                    flag = True
                    break
                table[j] = turn_right(table[j])
            if flag:
                flag = False
                break
    return result

def solution(game_board, table):
    # 1. 조각을 구한다.
    game_pieces = get_pieces(game_board)
    for i in range(len(table)):
        for j in range(len(table)):
            table[i][j] ^= 1
    table_pieces = get_pieces(table)

    # 2. 사이즈별로 분류된 조각을 하나씩 맞춰본다.
    answer = 0
    for size in range(1, 7):
        answer += check(game_pieces[size], table_pieces[size], size)

    return answer

print(solution(
    [[1,1,0,0,1,0],[0,0,1,0,1,0],[0,1,1,0,0,1],[1,1,0,1,1,1],[1,0,0,0,1,0],[0,1,1,1,0,0]],
    [[1,0,0,1,1,0],[1,0,1,0,1,0],[0,1,1,0,1,1],[0,0,1,0,0,0],[1,1,0,1,1,0],[0,1,0,0,0,0]]))
```