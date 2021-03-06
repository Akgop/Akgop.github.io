---
title: \[MySQL] 1. DBMS 개요와 MySQL 소개
categories: 
  - mysql
tags: 
toc: true
---

*한빛미디어의 이것이 MySQL이다를 수강하며 정리한 포스트* 

### MySQL

---

MySQL은 DBMS 소프트웨어의 일종

### 데이터베이스 정의와 특징

---

데이터베이스는 '데이터의 집합'이다. 데이터베이스는 여러 명의 사용자가 **동시 접근**이 가능해야 한다. → Excel이 DBMS로 불리지 않는 이유는 동시에 접근이 되지 않기 때문이다.

DBMS(Database Management System)는 데이터베이스를 운용하기 위한 시스템, 소프트웨어이다.

- **데이터의 무결성:** 데이터베이스 안의 데이터는 어떤 경로를 통해 들어왔던지 데이터에 오류가 있으면 안된다
- **데이터의 독립성:** 데이터베이스의 크기를 변경하거나, 데이터 파일의 저장소를 변경하더라도 다른 응용프로그램의 내용을 바꾸면 안된다. 즉, 프로그램과 데이터베이스는 각각 독립적으로 작동해야 한다.
- **보안**: 아무나 접근할 수 없게 하고, 허용된 사용자만, 제한된 권한만 가지고 접근할 수 있도록 하는 것
- **데이터 중복의 최소화:** 동일한 데이터가 중복되는 것을 최소화 하도록 한다.
- **응용 프로그램 제작 및 수정이 쉬워짐:** 데이터베이스를 구성하고 나면 공통적인 프로그램이 가능해진다. 유지보수 또한 쉬워진다.
- **데이터의 안전성 향상:** DBMS가 제공하는 백업, 복원 기능을 이용함으로써 데이터가 깨지는 문제가 발생해도 원상으로 복구할 수 있다.

### 데이터베이스의 발전

---

오프라인 → 파일 시스템(메모장 등) → 데이터베이스 관리 시스템(DBMS)

파일시스템의 단점을 보완하고 대량의 데이터를 보다 효율적으로 관리하고 운영하기 위해서 사용되는 것이 DBMS

DBMS는 데이터의 집합인 데이터베이스를 잔 관리하고 운영하기 위한 시스템, 또는 소프트웨어를 말한다. DBMS에 데이터를 구축하고 잘 관리하고 활용하기 위해서 사용되는 언어가 **SQL**(Structured Query Language)이다. 이 SQL을 사용해서 우리가 DBMS를 통해 중요한 정보들을 입력하고 관리하고 추출할 수 있다.

### 관계형(Relational) DBMS

---

RDBMS의 핵심 개념은 테이블(Table)이라는 최소 단위로 구성되어 있다. 테이블은 표 형태로 되어있고 행(Row)과 열(Column, Field)로 이루어져있다.

테이블은 데이터를 효율적으로 저장하기 위한 구조이다. 정보를 저장하기 위해서 하나의 테입르이 아닌 여러 개의 테이블로 나누어서 저장함으로써 공간의 낭비를 줄이고, 효율성을 보장해 줄 수 있다.

테이블의 관계를 기본 키(Primary Key)와 외래 키(Foreign Key)를 사용해서 맺어 줌으로써, 두 테이블을 부모와 자식의 관계로 묶어 줄 수 있다. 추후에 부모 테이블과 자식 테이블을 조합해서 결과를 얻고자 할 경우에는 SQL의 JOIN 기능을 이용하면 된다.

RDBM의 장점

- 업무가 변화될 경우에 쉽게 순응할 수 있는 구조, 유지보수 측면에서 편리
- 대용량 데이터의 관리와 데이터의 무결성의 보장을 잘 해줌

단점

- 시스템 자원을 많이 차지해서 시스템이 전반적으로 느림 → 하드웨어의 발전으로 보완되고 있긴 함

### SQL(Structured Query Language) 간략

---

SQL은 RDBMS에서 사용되는 언어. 회사마다 각각 특색이 있지만 표준 SQL이 정립되어있다.

특징

- DBMS제작 회사와 독립적이다
- 다른 시스템으로 이식성이 좋다
- 표준이 계속 발전한다
- 대화식 언어이다
- 분산형 클라이언트/서버 구조이다

### MySQL

---

MySQL은 Oracle사에서 제작한 DBMS 소프트웨어, 오픈 소스