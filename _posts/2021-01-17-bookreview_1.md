---
title: \[책] 비전공자를 위한 이해할 수 있는 IT지식
categories: 
  - BookReview
toc: true
---

나도 공부를 하면서 큰 숲을 잊고 나무만 보면서 공부할 때가 있다. 따라서 전체적인 틀을 복습하기 위해 읽어보았고, 몰랐던 내용과 까먹기 쉬운 내용들을 간략히 정리했다. (복기 용도)

### 와이어 프레임

---

애플리케이션이나 웹 페이지가 어떻게 구성되는지, 어떻게 동작하는지 기획한 문서

기획자가 하는 일 중 하나

### RESTful API

---

REST = Representational State Transfer

체계적인 API, 즉 CRUD를 하나의 주소로 관리한다

- Create : POST
- Read : GET
- Update : PUT(전체) / PATCH (일부)
- Delete : DELETE

[BitMEX API Explorer](https://testnet.bitmex.com/api/explorer/)

비트코인 자동매매 프로그램 구현할 떄 사용했던 RESTful API 문서

이렇게 문서가 공개되어있는 곳도 있다. 개발에 참고할 수 있음

### HTTP 응답 상태 코드

---

CRUD의 Read시 응답의 통일을 위해 고안된 번호 체계

- ex): 404 등

### SDK(Software Development Kit)

---

API를 제공해주는 다른 소프트웨어, 즉 소프트웨어를 개발하기 위한 도구

[SDK란?](https://www.redhat.com/ko/topics/cloud-native-apps/what-is-SDK)

더욱 자세한 내용은 위 링크를 참고

### JSON

---

데이터를 교환하는 형식

과거에는 XML이 현재는 JSON이 대세.

JSON은 중괄호로 시작, key-value로 이루어져있고 colon(:)으로 구분

.json이라는 파일이 존재

### DNS(Domain Name System)

---

IP주소와 www.~ 을 맵핑해주는 서비스

### 웹

---

HTML: Hypertext Markup Language

CSS: Cascading Style Sheet

HTML과 CSS를 합쳐서 '퍼블리싱' 작업 → 퍼블리셔

### 웹 파편화(브라우저 버전 파편화)

---

[웹브라우저 파편화, 해결될 수 있을까? - 파편화 원인과 개선을 위한 노력](https://www.samsungsds.com/kr/insights/web_browser.html)

수많은 웹 버전과, HTML, CSS, Javascript 버전에 의해 버전 동기화, 획일화가 안되고 지원되는 범위가 달라 생기는 문제

웹개발자, 퍼블리셔들이 겪는 어려움중 하나

### 반응형 웹

---

화면 크기에 맞게 브라우저 크기가 변한다.

- bootstrap이 대표적

### 데이터베이스

---

데이터의 무결성: 데이터는 단 1%의 결점도 없어야 한다.

RDB: Relational Database

RDBMS: RDB management system, 관계형 데이터베이스 관리 시스템 → MySQL, Oracle등