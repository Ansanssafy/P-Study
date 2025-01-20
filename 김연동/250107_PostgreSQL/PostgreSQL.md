# PostgreSQL
<img src="./img/1.png" alt="Image Description" width="250">

- SQL(관계형)과 JSON(비관계형) 쿼리를 모두 지원하는 오픈소스 관계형 데이터베이스
- 오늘날 규정을 가장 잘 준수하고, 안정적이며 성숙한 관계형 데이터베이스 중 하나
- 세계에서 가장 많은 기업과 개발자들이 사용


## 왜 PostgreSQL을 사용하나?
- PostgreSQL은 확장 가능하고 다양한 기능을 제공
- 시계열 데이터부터 Geospatial Analytics에 이르기까지 광범위한 확장 에코시스템을 통해 다양한 특수 사용 사례를 신속하게 지원
-  많은 기업에게 "모든 것을 충족하는" 솔루션

## PostgreSQL 특징
### 1. 오픈 소스
- 누구나 코드에 접근 및 수정 가능
### 2. ACID 준수
- 원자성, 일관성, 격리성 지속성을 준수하여 데이터의 일관성과 신뢰성을 보장
### 3. 표준 SQL 준수
- ANSI SQL 표준을 따르며, 표준 SQL 기능을 거의 완벽하게 지원
### 4. 확장성
- 함수, 데이터 타입, 연산자 등이 확장가능하여 요구사항에 맞게 DB기능 확장 가능
### 5. 다양한 데이터 타입
- 기본적인 데이터 타입 외에도 JSON, Hstore, XML 등 비정형 데이터 저장 가능
- https://velog.io/@10000doo/PostgreSQL-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%83%80%EC%9E%85-%EC%A0%95%EB%A6%AC
### 6. 복잡한 쿼리 지원
- 하위 쿼리, 복잡한 조인, CTE(Common Table Expression) 등 고급 SQL 쿼리 기능 지원
### 7. 트리거와 저장 프로시저
- 데이터를 처리하기 전후에 자동으로 실행되는 트리거와 저장 프로시저를 지원
- 데이터 처리 로직을 서버측에서 관리 가능
* 트리거
  - DB에서 특정 이벤트 발생할 때 저당으로 저장된 절타 트리거
* 프로시저
  - DB에서 미리 컴파일되어 저장된 SQL문의 집합
## 주요기능
### 1. MVCC(다중 버전 동시성 제어)
- 트랜잭션의 격리성과 동시성을 보장
- 데이터의 일관성을 유지하기 위해, 동시에 여러 사용자가 데이터를 읽고 쓰기 가능 
### 2. 복제 및 클러스터링
- 스트리밍 복제를 통해 데이터를 여러 서버에 실시간으로 복제하여 안정성과 확장성을 높임
### 3. 파티셔닝
- 큰 테이블을 여러 개의 작은 테이블로 나누어 관리하여 성능 최적화
### 4. JSON 및 NoSQL 기능
- JSON형식의 데이터를 저장하고 쿼리 할 수 있음
- NoSQL DB의 장점을 결합한 하이브리드 환경 지원
### 5. 전문 검색
- 텍스트 검색을 위한 Full-Text Search 기능 제공
- 문서 내 검색을 효율적으로 수행 가능

## MySQL vs PostgreSQL
### ACID 준수
- MySQL은 InnoDB 및 NDB 클러스터 스토리지 엔진 or 소프트웨어 모듈과 함께 사용하는 경우에만 ACID 규정 준수
- PostgreSQL은 모든 구성에서 ACID와 완벽하게 호환
### 동시성 제어
- 레코드의 중복 사본을 생성하여 동일한 데이터를 병렬로 안전하게 읽고 업데이트하는 고급 데이터베이스 기능
- MVCC를 사용하면 여러 사용자가 데이터 무결성을 손상시키지 않고 동일한 데이터를 동시에 읽고 수정
- MySQL은 이 기능 지원 안함
### 인덱스
- 인덱스를 사용하여 데이터를 더 빠르게 검색
- 자주 액세스하는 데이터를 다른 데이터와 다르게 정렬 및 저장
- 인덱스 유형에는 트리, 표현식 인덱스, 부분 인덱스 및 해시 인덱스가 포함
### 데이터 유형
- MySQL은 순수 관계형 DB, PostgreSQL은 객체 관계형 DB
- 즉, PostgreSQL에서는 데이터를 속성을 가진 객체로 저장가능
### 보기
- MySQL은 보기를 지원, PostgreSQL은 고급 보기 옵션 제공
- 일부 값을 미리 계산하여 구체화된 뷰를 생성하며 구체화된 뷰는 복잡한 쿼리의 DB 성능을 향상
### 저장 프로시저
- 미리 작성하고 저장할 수 있는 구조화된 쿼리 언어(SQL)
- MySQL과 PostgreSQL 모두 저장 프로시저를 지원
- PostgreSQL을 사용하면 SQL 이외의 언어로 작성된 저장 프로시저를 호출 가능
### 트리거
- DB 관리 시스템에서 관련 이벤트가 발생할 때 자동으로 실행되는 저장 프로시저
- MySQL에서는 SQL INSERT, UPDATE 및 DELETE 문에 AFTER 및 BEFORE 트리거만 사용 가능
-  PostgreSQL은 INSTEAD OF 트리거를 지원하여 함수를 사용하여 복잡한 SQL 문을 실행 가능

```SQL
INSERT INTO topic (title,body) VALUES('Select','Select is...');
INSERT INTO topic (title,body) VALUES('Update','Update is...');
INSERT INTO topic (title,body) VALUES('Delete','Delete is...');

SELECT * FROM topic;

SELECT id, title FROM topic;

SELECT id, title FROM topic WHERE id = 1;
SELECT id, title FROM topic WHERE id <> 1;
SELECT id, title FROM topic WHERE id > 2;

SELECT id, title FROM topic WHERE id > 1 ORDER BY id DESC;
SELECT id, title FROM topic WHERE id > 1 ORDER BY id ASC;

SELECT id, title FROM topic WHERE id > 1 ORDER BY id ASC LIMIT 2;

UPDATE topic SET title = 'Delete2', body = 'Delete2 is...' WHERE id = 4

DELETE FROM topic WHERE id = 4;
```
### JSON
```SQL
[
  {
    "id": 1,
    "title": "Select",
    "body": "Select is..."
  },
  {
    "id": 2,
    "title": "Update",
    "body": "Update is..."
  },
  {
    "id": 3,
    "title": "Delete",
    "body": "Delete is..."
  }
]

```

# PostgreSQL vs MySQL vs MongoDB 비교

## 데이터베이스 비교 표

| **특징**              | **PostgreSQL**                     | **MySQL**                       | **MongoDB**                   |
|-----------------------|------------------------------------|----------------------------------|-------------------------------|
| **데이터 모델**        | 관계형 (RDBMS)                    | 관계형 (RDBMS)                  | 비관계형 (NoSQL, 문서 기반)    |
| **확장성**            | 뛰어남 (JSONB, 사용자 정의 타입)   | 뛰어남                          | 매우 뛰어남 (샤딩 지원)        |
| **트랜잭션 지원**     | 완벽 (ACID)                       | 완벽 (ACID)                     | 기본적 수준 (단일 문서 기준)  |
| **읽기 성능**         | 적당히 우수                       | 매우 우수                       | 매우 우수                     |
| **JSON 처리**         | JSON/JSONB 지원                   | 제한적 JSON 지원                | 완벽한 JSON 처리               |
| **복제 및 클러스터링** | 스트리밍 복제, 논리 복제 지원       | 간단한 복제 설정                | 샤딩 및 복제 우수             |
| **NoSQL 활용**        | 일부 가능 (JSONB)                 | 불가능                          | 완벽                          |

## 데이터베이스 선택 기준

### PostgreSQL
- **장점**
  - 관계형 데이터와 비정형 데이터(JSONB)를 모두 처리 가능.
  - 고급 트랜잭션과 데이터 무결성 기능 제공.
  - 사용자 정의 함수와 확장 기능(PostGIS 등)을 통한 높은 확장성.
- **적합한 상황**
  - 관계형 데이터와 JSON 데이터를 함께 처리해야 하는 프로젝트.
  - 복잡한 트랜잭션과 SQL 표준 준수가 중요한 경우.

### MySQL
- **장점**
  - 간단한 설정과 뛰어난 읽기 성능.
  - 많은 기존 시스템에서 사용되고 있어 호환성이 뛰어남.
- **적합한 상황**
  - 간단한 읽기 작업 위주의 애플리케이션.
  - 기존 MySQL 기반 프로젝트와의 호환이 필요한 경우.

### MongoDB
- **장점**
  - 완전한 비정형 데이터 저장 및 처리.
  - 기본적으로 샤딩과 복제를 지원하여 대규모 데이터 처리에 적합.
- **적합한 상황**
  - 스키마가 고정되지 않은 데이터 모델이 필요한 경우.
  - 대규모 데이터를 처리하거나 분산 시스템을 구축해야 하는 경우.

---

## 결론
- PostgreSQL은 관계형 데이터베이스와 JSON 처리가 모두 가능한 강력한 데이터베이스로, 대부분의 요구사항을 충족
- MySQL은 간단한 설정과 높은 읽기 성능이 중요한 경우 적합
- MongoDB는 비정형 데이터 처리와 대규모 분산 시스템이 필요한 경우 적합



