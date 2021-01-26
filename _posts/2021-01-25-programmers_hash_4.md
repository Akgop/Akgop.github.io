---
title: \[프로그래머스] 베스트앨범
categories: 
  - programmers
tags: 
  - hash
---

### 해시

---

정렬 기준이 많고 까다로워서 구현 난이도 자체가 어려운 문제

설명은 소스코드의 주석으로 대체

```python
def solution(genres, plays):
    answer = []
    n = len(genres)   # 장르의 개수를 n으로 설정
    table = dict()    
    # {"key": [[-1, total], [고유번호, play], [고유번호, play]]
    # 첫 원소로 total값을 계속 더해주어 나중에 장르 순서로 정렬하기 좋게 구현
		for i in range(n):
        # 이미 테이블에 해당 장르가 있다면
        if genres[i] in table:
            # 해당 장르 total play 연산
            table[genres[i]][0][1] += plays[i]
            # dictionary의 value list 불러와서
            tmp_list = table[genres[i]]
            # 상위 2개가 아직 차지 않았다면 그냥 더함
            if len(tmp_list) < 3:
                tmp_list.append([i, plays[i]])
            # 상위 2개가 이미 차있고 새로운 play가 같은 카테고리에 들어간 경우
            else:
                # play수가 기존 가장 작은 애보다 높으면 대체
                if tmp_list[2][1] < plays[i]:
                    tmp_list[2][0] = i
                    tmp_list[2][1] = plays[i]
            # 대체하고 정렬
            tmp_list.sort(key=lambda x: x[1], reverse=True)
        # 테이블에 해당 장르가 아직 없는 경우 initialize
        else:
            table[genres[i]] = [[-1, plays[i]], [i, plays[i]]]
    # 그렇게 해서 구현된 value list를 추출
    value_list = list(table.values())
    # total play 값으로 내림차순 정렬
    value_list.sort(key=lambda x: x[0][1], reverse=True)
    # answer에 값 옮긴다. 1개이거나 2개일 수 있으므로 len(e)로 구현
    for e in value_list:
        for i in range(1, len(e)):
            answer.append(e[i][0])
    return answer
```