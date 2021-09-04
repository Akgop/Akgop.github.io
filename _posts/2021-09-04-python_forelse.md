---
title: \[Python] for ~ else
categories: 
  - python
tags: 
  - for
toc: true
---

코딩 테스트를 풀다가 새로운 문법을 알게되서 기억하기 위해서 작성한다.

기존에 for문이 중간에 종료되었을 경우를 판별하기 위해서는 flag와 같은 특정 변수를 통해 판별해주곤 했다.

그러나 아래와 같이 중간에 break이 걸리지 않고 for문이 끝까지 완수된 경우 수행해야 하는 코드가 있는 경우, else문을 이용해서 처리를 해보자.

```python
flag = True
for element in li:
    if element > 5:
        flag = False
        break
if flag:
    print("done")
```

위의 코드와 아래 코드는 동일하게 수행된다.

```python
for element in li:
    if element > 5:
        break
else:
    # for 문이 완전히 수행되었을 경우에 대한 처리를 해줘야 하는 경우 작성
    print("done")
```