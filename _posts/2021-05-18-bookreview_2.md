---
title: \[책] 대규모 서비스를 지탱하는 기술
categories: 
  - BookReview
tag:
  - Memory
  - Disk
toc: true
sticky: true
---

> 대규모 서비스를 지탱하는 기술이라는 책을 읽고 정리해보았다. 항상 컴퓨터 공학과 수업을 들으며 이것이 실제 서비스에서 어떻게 쓰이는지 궁금했다. 백엔드 서버 개발자가 되고자 하는 지망생의 입장에서 정말 중요하다고 생각되는 내용들이 많았다. CS지식을 더욱 깊게 다시한번 생각해보는 계기가 되었다.

## 대규모 서비스에서 발생하는 문제점

"데이터량"이 많기 때문에 부하가 발생한다. 디스크 → 메모리 → 캐시 → CPU

- 확장성 확보, 부하분산 필요
- 다중성 확보
- 효율적 운용
- 개발자 수, 개발 방법의 변화

## 디스크 vs 메모리

탐색 시간(seek time): 내부에서 정보를 찾아내는 시간

- 디스크(HDD): 개느리다. why? → 디스크는 물리적으로 원판을 돌며 정보를 찾아야한다.
- 디스크(SSD): 원판을 돌며 찾는 구조가 아니라서 많이 개선되서 빠르다.
- 메모리: 전기적 신호로 빠르게 찾아낼 수 있다. 하드디스크보다 10만~100만배 빠르다.

전송 시간(transfer time): 디스크 → 메모리, 메모리 → CPU 등 실제 데이터를 접근해서 통로에서 보내는 시간

- 디스크(HDD, SSD): 둘 다 결국 느리다. 회선적 문제로
- 메모리: 개빠르다. 디스크보다 100배 빠르다.

## CPU 부하 vs I/O 부하

CPU 부하란?

- CPU 작업을 오지게 돌리는 경우 → 프로그램을 잘못 짜서 무한루프 돌거나, 동영상 데이터처럼 다룰 것이 많거나, 암튼 CPU의 가용률이 너무 높은 경우
    - 프로그램 로직 개선 → 불가능 할 경우 서버 증설

I/O 부하란?

- 프로그램으로부터 입출력이 너무 많아서 혹은 Swap행위를 통한 디스크 액세스가 너무 많이 발생하는 경우
    - 언제 Swap이 많이 발생하는지 측정을 하고, 어떤 프로세스가 메모리를 지나치게 잡아서? → 프로그램(코드) 개선이 필요한 상황!
    - 결국 그냥 메모리가 부족한 경우도 있기에 → 메모리 증설로 해결!
- Swap도 아닌데 디스크 액세스가 많은 경우
    - Swap도 아닌데? → 캐시, 메모리에 정보가 부족하다!! → 메모리 증설로 캐시 영역 확보

OS튜닝이란?

- 위 부하들을 상황에 맞게 파악하고, 알맞게 필요한 부분만 고치는 것!

CPU 부하 vs I/O 부하 

- CPU 부하는 결국 스케일 아웃(서버 증설 = 스케일 업에 비해 저렴)을 통해 해결하고, 로드밸런서 도입해서 처리하면 된다.
- I/O 부하는 결국 write해야 하는 경우, 즉 무언가를 변경해야 하는 경우 서버를 증설해서 데이터를 분산하여 처리하기에는 간단한 내용이 아니다. → 데이터 동기화 문제, 디스크는 개느리다 등

→ 결론: **대규모 데이터는 메모리에서 처리하기 어렵고, 마냥 분산하기도 곤란하다.**

→ 번외: swap이란 메모리 공간이 부족하여 "프로세스" 단위로 잠시 메모리에서 내쫓는 것을 의미(스왑 영역에 배치) 그리고 다시 호출을 하면 가져오는 원리. paging과 다른 점은, paging은 페이지 단위로 page fault가 발생할 경우 가져오는 것이고, 스왑은 프로세스 단위로 조절한다는 점.

## OS 캐시

OS 캐시란?

- OS가 메모리에 필요한 정보를 미리 올려놓는 것. 즉 디스크 참조를 줄이기 위한 기법 → page cache 등

가상 메모리

- 프로세스에서 메모리를 다루기 쉽게 한다. HOW? → 모든 프로세스가 데이터 위치를 명시적으로 알고있지 않아도 된다. 변환 테이블과 기법만 있으면 된다.
- OS가 커널 내에서 메모리를 추상화 하고 있다.
- 페이지(page): OS가 한번에 처리하는 가상 메모리 단위
- 프레임(frame): page와 동치되는 물리 메모리 단위.
- 페이징(paging): 가상 → 물리 메모리 변환 기법

→ 결국 위 내용을 알아야 하는 이유는 I/O 부하를 줄이기 위함을 다시 한번 상기하자.

## I/O 부하 줄이기

OS 캐싱을 통해 해결이 가능한 경우

- 메모리를 늘림 → 캐시가 사용할 수 있는 용량 증가 → 보다 많은 데이터를 캐싱 → 디스크 액세스 횟수 절감(I/O 부하 절감)
- 데이터 압축을 해서 저장.

OS 캐시로 해결될 수 없는 규모인 경우

- scale-out(서버 증설)
    - CPU-bounded 서버의 경우 단순히 처리를 분산하면 되기 때문에 서버 증설 및 로드 밸런싱(앞서 말한것과 동일)
    - I/O-bounded 서버의 경우 불가능 → 국소성을 고려(locality)

## locality를 고려한 분산(I/O 부하 분산)

파티셔닝(partitioning): DB 서버를 여러 대의 서버로 분할하는 방법

- 테이블 단위 분할 → 같이 액세스 하는 경우가 많은 애들끼리 묶어서 요청 엔트리를 나눔
- 테이블 데이터 분할 → 내부 데이터에 따라 나눔 (ex: a~f는 1번 서버, g~z는 2번 서버 등)
- 요청 패턴에 따라 섬으로 분할 → 국소성이 두드러지는 애들을 묶어서 하나의 섬으로, 검색엔진처럼 국소성이 떨어지는애들을 하나의 섬으로(느려도 되는 경우거나)

→ 결론: OS 동작 원리를 이해하는게 대규모 서비스 부하분산의 핵심이다. 아래 4개는 마스터 하자.

- OS 캐시
- 멀티스레드, 멀티프로세스
- 가상 메모리 구조
- 파일 시스템

파티셔닝의 장점?

- 부하가 내려감
- 국소성이 증가 → 캐시 효과 높아짐.

파티셔닝의 단점?

- 운용이 어렵다. 사람이 머리로 어디에 뭐가 있는지 알아야 한다
- 고장률이 올라간다. 기기가 많아질수록 고장률이 올라가는건 당연하다.
- 굳이 파티셔닝을 안해도 메모리가 쌀 수도? trade-off를 고려해봐야함.

## DB 서버 분산을 고려한 프로그래밍

MySQL 기준으로,

- OS 캐시 활용 → 앞선 설명 그대로
- 인덱스(Index): B+ Tree.
    - B+ Tree는 나누는 노드의 크기를 정할 수 있다. m = 5 → 한번 내려가서 datanode의 크기가 5라던가.
    - 이걸 4KB(페이지의 기본 단위라고 가정, 1블락) 라고 한다면? → OS 는 디스크에서 데이터를 블록 단위로 읽어낸다. 즉, OS가 한번에 읽어내서 메모리에 캐싱하게 되므로 같은 노드 내의 데이터는 디스크 탐색 없이 메모리에서 검색할 수 있다! → 디스크 탐색 횟수를 현저히 줄인다.(순차탐색이나 일반 binary tree에 비해)
- 확장을 전제로 시스템을 미리 설계 → 정규화를 미리 하거나.

## MySQL 레플리케이션(replication)을 통한 분산

레플리케이션이란?

- 마스터(master)를 정하고 마스터를 뒤따르는 서버(slave)를 정하면, 마스터에 쓴 내용을 슬레이브가 폴링(polling)해서 동일한 내용으로 자신을 갱신하는 기능.

어떻게 레플리케이션을 이용해서 분산하는가?

- AP 서버에서 생긴 쿼리를 로드밸런서에 넣는다
- select같은 read 쿼리는 복제된 여러 slave서버로 넣는다.
- insert, delete, update같은 write 쿼리는 master에 직접 넣는다 → slave가 polling하여 업데이트
- 이는 O/R 매퍼로 제어한다.

master는 분산이 안되는데요?

→ 웹 페이지 특성상 read 쿼리가 1000배 많이 발생한다고 한다.

그래도 분산하고 싶은데요?

→ 앞서 본 테이블 단위, 테이블 데이터 단위 등 분할하거나 NoSQL DB서버를 사용해보는 것도 방법 (key-value storage)

→ 결론: MySQL 의 scale-out 전략: 메모리에 최대한 올리고, 메모리 증설해보고, 인덱스를 제대로 걸어서 디스크 탐색을 최대한 줄인다. 메모리 증설이 안되면 파티셔닝!

## 용도특화형 인덱싱

RPC(Remote Procedure Call) = Web API

- 서버에 쿼리를 던져 JSON을 반환하는 방식 → RDBMS 한계가 있기 때문에 별도 인덱스 서버를 만드는 것.

검색 처리(자연어처리)

- Trie 기반의 정규표현을 사용하여 Common Prefix Search(공통접두사검색)
- Aho-Corasick법, Double Array Trie등을 사용한 알고리즘/데이터 구조를 DB에서 사전에 추출하여 만들어두고 매칭하는 것이 키워드 링크의 방법이라고 한다.

→ 결론: RDBMS에서의 정보 검색은 데이터를 배치로 얻고, 역 인덱스를 만들어서 검색 알고리즘을 사용.

## 웹 서비스의 인프라

웹 서비스의 인프라에서 가장 중요한 3가지 포인트

- 저비용 고효율: 굳이 신뢰도가 100%일 필요가 있을까? → 차라리 더 싸게 효율적으로.
- 확장성, 응답성을 미리 고려하여 설계 및 대비
- 개발속도: 무조건 기동성있게 대처해야한다. 트래픽이 많아지면 서버를 바로 추가하여 가동할 수 있을 정도로 빠른 템포로.

기술적으로는?

- 확장성
- 다중화
- 효율향상
- 네트워크

## 클라우드 컴퓨팅

클라우드 컴퓨팅이란?

- 인프라를 빌려서 운영하는 것 → 저비용 고효율!!!!

장점

- 저가로 사용하며 확장해갈 수 있다. → 필요할때마다 추가로 확장할 수 있다는 것. == 확장성!!

단점

- 미리 준비된 클라우드 시스템에 성능을 맞춰야댐. → I/O 성능이 그다지 원하는만큼 빠르지 않을 수도 있음
- 구체적인 스펙을 알기 힘듬.
- 이제는 해결되지 않았나? (책이 오래댐 10년전 이야기)

반대로 자체 구축 인프라의 장점!

- 하드웨어 구성을 유연하게 가져간다. → 따로 메모리만 증설한다거나.
- 서비스 요청에 대해 유연하게 대응가능
- 병목현상을 제어할 수 있다. → 로드밸런서, 네트워크 회선 등.

## AP 서버의 다중성 확보

AP 서버는 다중화 하기 쉽다. → why? CPU부하가 클 확률이 높으므로!

그렇다면 여러대를 놓았을 경우 문제점은? → 고장이 많아진다!

1, 2 대 정도가 고장났을 때 어떻게 계속 처리할 것인가? → failover, failback을 어떻게 할 것인가!

페일 오버(failover)

- 고장난 서버를 로드밸런서가 자동적으로 분리하는 처리

페일 백(failback)

- 서버가 복구되면 원상태로 복귀시키는 처리

## DB 서버의 다중성 확보

앞서 본 그대로 마스터-슬레이브 구조를 통해 슬레이브를 다중화 한다.

마스터 다중화는 멀티 마스터라는 방법을 이용할 수도 있다.

멀티 마스터

- 마스터끼리 쌍방으로 레플리케이션을 하도록 구축한다. 서로 마스터끼리 polling을 하여 write가 일어나면 동기화 하는 것이다!
- 그러면 수 ms동안 동기화가 안되는 지점이 있을텐데 그 동안 불일치가 일어나면 어떡하나? → 레플리케이션에 lock을 걸어 동기적 처리를 하도록 한다! or 그냥 불일치 발생할때 수동으로 처리한다.
- lock을 걸어 동기적으로 처리하면 → 성능이 줄어든다!! → 성능과 정확도의 trade-off관계. 이건 케바케로 구현한다. 보통 성능을 중시하는 경우가 많다.
- 자세한 내용은 Active/Standby로 구성되고, VRRP(Virtual Router Redundancy Protocol)로 감시한다는 것 이지만 다루지 않겠다.(어렵)

## 시스템 안정화 대책

적절한 버퍼 유지

- CPU를 95%씩 가동하다가 서버 하나가 다운된다. → 다른 서버에게 부담이 늘어난다 → 전체가 다운될 수 있다. → 따라서 모든 각 서버 부하를 70% 대로 유지한다. → 다른 서버가 다운이 되도 안정적으로 일을 수행할 수 있다. 복구가 될 때까지.

불안정 요인 제거

- SQL 부하: DB에 이상한 SQL을 날리면 바로 멈추면서 다운되버린다.(부하가 높은 SQL)
    - 부하가 높은 SQL만을 별도로 처리하는 서버를 두어 일반 SQL에 피해주지 않게 한다. 즉 DB를 분리해서 처리하자.
- 메모리 누수 줄이기 → 당연한 프로그래머의 소양!
- 비정상 동작 시 자율 제어
    - 자동 DoS 판정 → 403 반환
    - 자동 재시작
    - 자동 쿼리 제거 → 코드 고치기 전 까지 SQL process KILL