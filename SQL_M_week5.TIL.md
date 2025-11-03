# SQL_MASTER 5주차 정규 과제 

## Week 5 : 인덱스

📌**SQL_MASTER 정규과제**는 매주 MySQL Workbench 툴을 활용하여 **직접 데이터베이스를 설계하고 실습하는 프로젝트 기반 과제**입니다. 

이번 주는 아래의 **SQL_MASTER_5th_TIL**에 나열된 주제를 중심으로 개념을 학습하고, 주차별 **학습 목표**에 맞게 정리해주세요. 정리한 내용은 GitHub에 업로드한 후, **스프레드시트의 'SQL' 시트에 링크를 제출**해주세요. 



> ✅ **과제 제출 시 반드시 “Workbench 실습 화면 (캡처)” 를 첨부해주세요!**
>
> - 테이블 생성, 수정, 삭제 명령이 실행된 화면 또는 결과가 확인되는 캡처를 첨부합니다.
> - Github에 정리하는 실습 TIL 링크를 **스프레드시트 'SQL_MASTER' 시트에 제출**해주세요.



## SQL_MASTER_5th_TIL

## 6장. 인덱스

### 01. 인덱스 개념을 파악하자

### 02. 제약조건으로 테이블을 견고하게 

### 03. 가상의 테이블 뷰





## 🏁 주차별 학습 (Study Schedule)

| 주차  | 공부 범위                  | 완료 여부 |
| ----- | -------------------------- | --------- |
| 1주차 | **데이터베이스와 SQL**     | ✅         |
| 2주차 | **실전용 SQL 미리 맛보기** | ✅         |
| 3주차 | **기본, 고급 DML 복습**    | ✅         |
| 4주차 | **테이블과 뷰**            | ✅         |
| 5주차 | **인덱스**                 | ✅         |
| 6주차 | **스토어드 프로시져**      | 🍽️         |
| 7주차 | **SQL 파이썬 연결하기**    | 🍽️         |

<br>

<!-- 여기까진 그대로 둬 주세요-->

---

# 1️⃣ 개념정리

## 01. 인덱스

~~~
✅ 학습 목표 :
* 인덱스의 개념과 종류를 이해할 수 있다.
* 인덱스의 작동 원리와 구조를 이해할 수 있다.
* 실제로 인덱스를 만들고 사용하는 방법을 따라할 수 있다. 
~~~

### 6-1 인덱스 개념을 파악하자

1. 인덱스란?

정의:
데이터를 빠르게 찾을 수 있도록 도와주는 도구
→ 책의 ‘찾아보기(index)’에 해당

없을 때:
데이터가 적을 땐 영향 미미하지만, 대용량 테이블에서는 성능 차이 큼

결과값:
인덱스 유무는 결과값의 차이를 만들지 않음 (단지 속도 차이만 존재)

| 구분 | 생성 시점 | 생성 가능 개수 | 정렬 여부 | 비유 |
|------|------------|----------------|------------|------|
| **클러스터형 인덱스 (Clustered Index)** | `PRIMARY KEY` 지정 시 자동 생성 | 테이블당 **1개만** 가능 | 자동 정렬됨 | 영어사전 (본문 자체가 정렬) |
| **보조 인덱스 (Secondary Index)** | `UNIQUE` 지정 시 자동 생성 | **여러 개** 생성 가능 | 자동 정렬되지 않음 | 일반 책의 찾아보기 (본문은 그대로, 인덱스만 추가) |

3. 인덱스의 장점

SELECT 검색 속도 비약적으로 향상

CPU/디스크 부담 감소 → 전체 시스템 성능 향상

4. 인덱스의 단점

추가 공간 차지: 약 테이블 크기의 10% 정도 필요

생성 시간 소요: 처음 인덱스 만들 때 시간이 걸림

데이터 변경 시 오히려 느려짐:
(INSERT, UPDATE, DELETE 작업이 많을 경우 성능 저하)

불필요한 인덱스는 오히려 해로움
→ “비타민도 과하면 독”

5. 자동 생성 인덱스

기본 키 지정 시:
→ 클러스터형 인덱스 자동 생성

고유 키(UNIQUE) 지정 시:
→ 보조 인덱스 자동 생성
```sql
CREATE TABLE member (
    mem_id CHAR(8) NOT NULL PRIMARY KEY,   
    mem_name VARCHAR(10) NOT NULL,
    mem_number INT NOT NULL
);
```

기본 키와 고유 키 모두 중복 불가 (Non_unique = 0)

6. 클러스터형 인덱스의 특징

테이블 내 데이터 자체가 정렬됨

정렬 기준 변경 시 테이블 전체 재정렬
→ 예시:
```sql
ALTER TABLE member ADD CONSTRAINT PRIMARY KEY(mem_id);
```

이후 mem_id 기준으로 자동 정렬됨

다른 열을 기본 키로 바꾸면
→ 새로운 기준으로 다시 정렬됨

7. 보조 인덱스의 특징

데이터 순서 변경 없음 (본문은 그대로, 인덱스만 추가됨)

여러 개 생성 가능
```sql
ALTER TABLE member ADD CONSTRAINT UNIQUE(mem_id);
ALTER TABLE member ADD CONSTRAINT UNIQUE(mem_name);
```

단, 인덱스가 많아질수록 저장 공간 증가 및 성능 저하 가능
→ “꼭 필요한 열에만 생성”이 원칙

8. 핵심 비유 요약

| 개념 | 비유 | 설명 |
|------|------|------|
| **인덱스 전체** | 책의 ‘찾아보기’ | 빠른 탐색용 도구 |
| **클러스터형 인덱스** | 영어사전 | 본문 자체가 정렬됨 |
| **보조 인덱스** | 일반 책의 찾아보기 | 본문 그대로, 뒤에 별도 목록 생성 |


## 06-2 인덱스의 내부 작동

### 🔹 핵심 키워드  
`균형 트리 (B-Tree)` · `페이지 (Page)` · `전체 테이블 검색` · `페이지 분할` · `인덱스 검색`

---

### 1. 인덱스의 내부 구조

- **클러스터형 인덱스**와 **보조 인덱스** 모두 내부적으로 **B-Tree (Balanced Tree)** 구조로 구성됨  
- **트리 구조 용어**
  - **루트 노드 (Root Node)**: 가장 상단의 출발점
  - **중간 노드 (Internal Node)**: 루트와 리프 사이의 노드
  - **리프 노드 (Leaf Node)**: 실제 데이터가 위치하는 마지막 노드  
- **MySQL 용어**
  - 노드(Node) = **페이지(Page)**
  - 한 페이지 크기 = **16KB (16384 byte)**
  - 데이터 1건만 있어도 최소 1페이지 필요

---

### 2. 균형 트리의 장점: 빠른 검색

- 트리 구조는 **정렬된 경로를 따라 탐색**하므로 불필요한 페이지를 읽지 않음  
- 예시:
  - 전체 검색 시 3페이지 읽던 것을  
  - 균형 트리에서는 **루트(1) + 리프(1)** → 총 2페이지만 읽어서 검색 완료  
- **데이터가 많을수록 효과 극대화**

---

### 3. 페이지 분할 (Page Split)

- 인덱스는 읽기(SELECT)는 빠르지만,  
  **입력(INSERT)** 시 페이지 분할 때문에 성능 저하 가능

**페이지 분할이란?**  
새 데이터를 삽입할 공간이 부족할 때,  
새 페이지를 만들어 기존 데이터를 분할 저장하는 작업

#### 💡 예시
| 상황 | 동작 | 영향 |
|------|------|------|
| 여유 공간 있을 때 | 기존 페이지에 그대로 삽입 | 빠름 |
| 여유 공간 없을 때 | 새 페이지 생성 → 데이터 분할 | 느려짐 |
| 루트까지 가득 찬 경우 | 루트도 분할되어 트리 단계 증가 | 매우 느려짐 |

즉, 인덱스가 많을수록 INSERT·UPDATE·DELETE가 느려지는 이유

---

### 4. 클러스터형 인덱스 구조

```sql
USE market_db;
CREATE TABLE cluster (
  mem_id CHAR(8),
  mem_name VARCHAR(10)
);
```
기본 키 지정 시 자동 정렬됨

루트 페이지와 리프 페이지(= 실제 데이터)로 구성

리프 페이지 자체가 데이터 페이지

영어사전처럼 본문 자체가 정렬된 형태


루트 페이지: 최상위 인덱스 -> 탐색 시작점

리프 페이지: 실제 데이터,데이터 자체가 정렬됨



5. 보조 인덱스 구조
```sql
USE market_db;
CREATE TABLE second (
  mem_id CHAR(8),
  mem_name VARCHAR(10)
);
```

본문(데이터 페이지) 는 그대로 유지됨

별도의 인덱스 페이지가 새로 생성됨

인덱스 페이지의 리프에는 정렬된 키 + 실제 데이터 위치가 기록됨

예시: APN → (1001번 페이지, #4 위치)

일반 책의 ‘찾아보기’처럼 본문은 그대로 두고, 뒤쪽에 인덱스 목록만 추가된 구조


인덱스 페이지: 키 값과 위치 저장,별도 공간 생성

데이터 페이지: 실제 데이터 저장,순서 변경 없음


## 06-3 인덱스의 실제 사용

### 1) 기본 문법

#### 인덱스 생성
```sql
CREATE [UNIQUE] INDEX 인덱스_이름
ON 테이블_이름(열_이름) [ASC | DESC];
```

인덱스 제거
```sql
DROP INDEX 인덱스_이름 ON 테이블_이름;
```

CREATE INDEX 로 만드는 것은 보조 인덱스
UNIQUE 를 붙이면 중복 불가 인덱스가 됨
정렬 방향은 기본이 ASC, DESC 는 드물게 사용

2) 확장 문법 개요

실무에선 대부분 아래 축약형을 사용
거
```sql
CREATE [UNIQUE] INDEX idx_name ON tbl(col);
거
```

MySQL의 상세 옵션 예시는 다음과 같지만 자주 쓰지 않음

```sql
CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX idx_name
USING {BTREE | HASH}
ON tbl (col [(length)] [ASC | DESC]) 
ALGORITHM = {DEFAULT | INPLACE | COPY}
LOCK = {DEFAULT | NONE | SHARED | EXCLUSIVE};

```

3) 자동 생성 인덱스와 제거

PRIMARY KEY 지정 시 클러스터형 인덱스 자동 생성

UNIQUE 제약 시 고유 보조 인덱스 자동 생성

자동 생성된 것은 DROP INDEX 로 제거하지 않고 ALTER TABLE 로 제약을 제거해야 함

-- 기본 키 제거
```sql
ALTER TABLE member DROP PRIMARY KEY;
```
-- UNIQUE 제약 제거
```sql
ALTER TABLE member DROP INDEX 인덱스_이름; 
```

4) 실습 스니펫
```sql
USE market_db;

-- 현재 인덱스 확인
SHOW INDEX FROM member;

-- 테이블/인덱스 크기 확인
SHOW TABLE STATUS LIKE 'member';

-- 보조 인덱스 생성 (중복 허용)
CREATE INDEX idx_member_addr ON member(addr);

-- 고유 보조 인덱스 생성 (중복 불가)
CREATE UNIQUE INDEX idx_member_mem_name ON member(mem_name);

-- 인덱스 메타 갱신
ANALYZE TABLE member;

-- 생성 결과 확인
SHOW INDEX FROM member;
SHOW TABLE STATUS LIKE 'member';
```

Index_length 는 보조 인덱스 용량, 최소 단위가 한 페이지 16KB 라서 작아도 16384로 보일 수 있음


5) UNIQUE 주의 사항

UNIQUE 로 만들면 기존 데이터에도 중복이 없어야 생성 가능

사람 이름처럼 중복 가능성이 있는 열은 UNIQUE 부적합

휴대폰 번호, 이메일 등은 UNIQUE 적합

예시

-- 중복이 있어 실패하는 예
```sql
CREATE UNIQUE INDEX idx_member_mem_number ON member(mem_number);
-- Error Code: 1062 Duplicate entry
```

6) 인덱스 활용과 실행 계획

인덱스가 있는 열이 WHERE 절에 등장해야 사용됨

VS Code나 Workbench의 Execution Plan 에서 확인

Full Table Scan → 인덱스 미사용

const, ref, range 등 → 인덱스 사용

예시
```sql
-- 동등 비교: 인덱스 사용
SELECT mem_id, mem_name FROM member
WHERE mem_name = '에이핑크';

-- 범위 조건: 인덱스 사용
CREATE INDEX idx_member_mem_number ON member(mem_number);
ANALYZE TABLE member;

SELECT mem_name, mem_number
FROM member
WHERE mem_number >= 7;
```

7) 인덱스를 사용하지 않는 경우

대부분의 행을 반환하는 조건

-- 사실상 전건 조회 → Full Table Scan이 더 유리할 수 있음
```sql
SELECT * FROM member WHERE mem_number >= 1;
```

열에 연산을 가한 조건

-- 인덱스 컬럼에 연산이 걸리면 인덱스 미사용
```sql
SELECT mem_name, mem_number
FROM member
WHERE mem_number * 2 >= 14;   -- mem_number >= 7과 동등하지만 스캔 발생
```

해결은 열을 그대로 두고 상수 쪽을 변형하기
```sql
WHERE mem_number >= 7;
```

8) 인덱스 제거 스니펫
```sql
-- 보조 인덱스 먼저 제거
DROP INDEX idx_member_mem_name ON member;
DROP INDEX idx_member_addr ON member;
DROP INDEX idx_member_mem_number ON member;

-- 기본 키 제거 전 외래키 의존성 확인
SELECT table_name, constraint_name
FROM information_schema.referential_constraints
WHERE constraint_schema = 'market_db';

-- 외래키 해제 후 기본 키 제거
ALTER TABLE member DROP PRIMARY KEY;
```

9) 효과적으로 쓰는 체크리스트

인덱스는 열 단위로 생성

WHERE 절에 자주 등장하는 열에 만든다

조회 빈도가 충분히 높을 때 의미가 있다

중복 값 종류가 너무 적은 열(예: 성별, 소수의 코드값)엔 효과 미미

클러스터형 인덱스는 1개만 가능, 조회에서 가장 자주 사용하는 키를 기본 키로

사용하지 않는 인덱스는 제거해 저장 공간과 쓰기 부하를 줄인다

인덱스 컬럼에 함수·연산을 걸지 않는다

대량 쓰기 위주 테이블은 인덱스 수를 최소화한다



<br>

---

# 2️⃣ 확인문제 & 추가학습

## 문제 1

> **🧚다음 아래의 테이블은 Member 테이블입니다.**

| **컬럼명** | **자료형** | **설명**    |
| ---------- | ---------- | ----------- |
| member_id  | INT        | 회원 번호   |
| email      | VARCHAR    | 이메일 주소 |
| name       | VARCHAR    | 이름        |

> **1️⃣ member_id를 클러스터형 인덱스 (Primary Key) 로 설정하는 SQL을 작성하세요.**
>
> **2️⃣ email을 고유 인덱스 (Unique Index) 로 설정하는 SQL을 작성하세요.**



~~~
-- 1. member_id를 클러스터형 인덱스(Primary Key)로 설정

ALTER TABLE Member
ADD CONSTRAINT PRIMARY KEY (member_id);

-- 2️. email을 고유 인덱스(Unique Index)로 설정
CREATE UNIQUE INDEX idx_member_email
ON Member (email);

~~~





## 문제 2

> **🧚다음 중 인덱스를 활용하기에 적합하지 않은 경우를 고르세요. 그리고 그 이유도 간단하게 작성해주세요.**

~~~
(보기)
① 데이터의 중복도가 매우 높은 컬럼 (예: 성별, Y/N)
② 자주 조회, 조인, 정렬에 사용되는 컬럼
③ 데이터가 매우 자주 변경되는 컬럼
④ 데이터의 범위 검색이 자주 일어나는 컬럼
~~~



~~~
① 중복도가 높은 컬럼

인덱스는 데이터를 걸러내는 역할을 하는데, 값이 거의 동일하면 검색 효율이 떨어짐

예: 성별, Y/N → 인덱스 탐색 후 결국 대부분의 행을 읽어야 함


③ 데이터가 자주 변경되는 컬럼

인덱스는 데이터 변경 시마다 갱신되어야 하므로

INSERT, UPDATE, DELETE가 잦은 컬럼에 인덱스를 두면 쓰기 성능 저하 발생
~~~



<br>

### 🎉 수고하셨습니다.
