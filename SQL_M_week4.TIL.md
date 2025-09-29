# SQL_MASTER 4주차 정규 과제 

## Week 4 : 테이블과 뷰

📌**SQL_MASTER 정규과제**는 매주 MySQL Workbench 툴을 활용하여 **직접 데이터베이스를 설계하고 실습하는 프로젝트 기반 과제**입니다. 

이번 주는 아래의 **SQL_MASTER_4th_TIL**에 나열된 주제를 중심으로 개념을 학습하고, 주차별 **학습 목표**에 맞게 정리해주세요. 정리한 내용은 GitHub에 업로드한 후, **스프레드시트의 'SQL' 시트에 링크를 제출**해주세요. 



> ✅ **과제 제출 시 반드시 “Workbench 실습 화면 (캡처)” 를 첨부해주세요!**
>
> - 테이블 생성, 수정, 삭제 명령이 실행된 화면 또는 결과가 확인되는 캡처를 첨부합니다.
> - Github에 정리하는 실습 TIL 링크를 **스프레드시트 'SQL_MASTER' 시트에 제출**해주세요.





## SQL_MASTER_4th_TIL

## 5장. 테이블과 뷰 

### 01. 테이블 만들기 

### 02. 제약조건으로 테이블을 견고하게 

### 03. 가상의 테이블 뷰





## 🏁 주차별 학습 (Study Schedule)

| 주차  | 공부 범위                  | 완료 여부 |
| ----- | -------------------------- | --------- |
| 1주차 | **데이터베이스와 SQL**     | ✅         |
| 2주차 | **실전용 SQL 미리 맛보기** | ✅         |
| 3주차 | **기본, 고급 DML 복습**    | ✅         |
| 4주차 | **테이블과 뷰**            | ✅         |
| 5주차 | **인덱스**                 | 🍽️         |
| 6주차 | **스토어드 프로시져**      | 🍽️         |
| 7주차 | **SQL 파이썬 연결하기**    | 🍽️         |

<br>

<!-- 여기까진 그대로 둬 주세요-->

---

# 1️⃣ 개념정리

## 01. 테이블과 뷰

~~~
✅ 학습 목표 :
* 테이블의 구조에 대해 완벽하게 이해할 수 있다. 
* 테이블의 핵심인 제약조건을 학습하고 적절하게 지정할 수 있다.
* 뷰의 개념과 실제 작동하는 방법에 대해 이해할 수 있다. 
~~~

# 테이블과 뷰

## 1. 테이블 기본 개념
- 테이블은 표 형태의 2차원 구조로, 행과 열로 구성됨  
- Microsoft Excel의 시트와 유사한 구조  



## 2. 회원 테이블 설계
회원 테이블은 다음과 같이 설계

| 열 이름(한글) | 열 이름(영문) | 데이터 형식 | 기타 |
|---------------|---------------|-------------|------|
| 아이디        | mem_id        | CHAR(8)     | PK, NOT NULL |
| 회원 이름     | mem_name      | VARCHAR(10) | NOT NULL |
| 인원수        | mem_number    | TINYINT     | NOT NULL |
| 주소          | addr          | CHAR(2)     | NOT NULL |
| 연락처 국번   | phone1        | CHAR(3)     | NULL |
| 전화번호      | phone2        | CHAR(8)     | NULL |
| 평균 키       | height        | TINYINT UNSIGNED | NULL |
| 데뷔 일자     | debut_date    | DATE        | NULL |



## 3. GUI 환경에서 테이블 만들기

### 3.1 데이터베이스 생성하기
1. MySQL Workbench 실행 → `root/0000` 계정 접속  
2. 새 쿼리 창 생성 후 다음 실행  
```sql
CREATE DATABASE naver_db;
```
3. 생성 직후에는 [SCHEMAS] 패널에 보이지 않음

4. [SCHEMAS] 패널의 빈 공간에서 우클릭 → Refresh All 클릭

### 3.2 회원 테이블 생성하기

1. MySQL Workbench에서 `[naver_db]` 데이터베이스를 확장합
2. **Tables** 항목에서 마우스 오른쪽 버튼 클릭 → **Create Table** 선택
3. 앞에서 설계한 대로 회원 테이블(member)을 구성

- 평균 키(`height`) → `TINYINT` 형식 지정, [UN] 옵션 체크(Unsigned)  
- 나머지 열도 설계된 대로 데이터 형식 지정  

4. 모든 설정이 끝났다면 [Apply] 버튼 클릭 → SQL 코드 확인 → [Apply], [Finish] 차례로 클릭하여 적용
5. 완료 후 `[File] - [Close Tab]` 메뉴를 실행하여 쿼리 창을 종료


### 3.3 구매 테이블 생성하기

1. 회원 테이블 생성과 동일하게 `[naver_db] - [Tables]`에서 마우스 오른쪽 버튼 클릭 → **Create Table**을 선택 
2. 구매 테이블(buy) 구성

- `num`: 자동 증가(AUTO_INCREMENT) 설정  
- `price`, `amount`: 음수가 들어가지 않도록 `UNSIGNED` 옵션 지정  

3. [Apply] 버튼 클릭 → 생성된 `CREATE TABLE` SQL 코드 확인 → [Apply], [Finish] 차례로 실행



#### 외래 키(FK) 지정
GUI 환경에서는 기본 키(PK)-외래 키(FK) 관계를 직접 선택할 수 없음
따라서 생성된 SQL 코드를 일부 수정해야함 

아래와 같이 `FOREIGN KEY` 구문을 추가

```sql
CREATE TABLE `naver_db`.`buy` (
    num INT AUTO_INCREMENT NOT NULL,
    mem_id CHAR(8) NOT NULL,
    prod_name CHAR(6) NOT NULL,
    group_name CHAR(4) NULL,
    price INT UNSIGNED NOT NULL,
    amount SMALLINT UNSIGNED NULL,
    PRIMARY KEY (num),
    FOREIGN KEY(mem_id) REFERENCES member(mem_id)
);
```

외래 키(FK)는 buy.mem_id가 반드시 member.mem_id에 존재해야 함을 의미

-> 회원으로 등록된 사용자만 구매 데이터를 입력 가능

### 4.1 회원 테이블 데이터 입력하기

1. MySQL Workbench에서 `[SCHEMAS]` 패널 → `[naver_db]` → `[Tables]` → `[member]`를 선택
2. 마우스 오른쪽 버튼 클릭 → **Select Rows - Limit 1000**을 선택
3. 새로운 SQL 탭이 열리며 `SELECT` 문이 자동 생성 
   - [Result Grid] 창에는 아직 데이터가 없으므로 빈 행이 표시됨
4. 상단의 **Insert new row()** 아이콘을 클릭하여 데이터를 입력
   - '네이버 쇼핑 DB 구성도'에 맞게 3개의 행을 입력 
5. 입력이 완료되면 우측 하단의 [Apply] 버튼을 클릭 → SQL 코드 확인 → [Apply], [Finish] 순으로 실행
6. 이후 `[File] - [Close Tab]` 메뉴를 실행하여 SQL 탭을 종료

GUI 환경에서는 직접 SQL을 작성하지 않고도 데이터를 입력할 수 있으며, 자동으로 `INSERT` 문이 생성됨


### 4.2 구매 테이블 데이터 입력하기

1. `[SCHEMAS]` 패널 → `[naver_db]` → `[Tables]` → `[buy]`를 선택
2. 마우스 오른쪽 버튼 클릭 → **Select Rows - Limit 1000**을 선택
3. 상단의 **Insert new row()** 아이콘을 클릭하여 '네이버 쇼핑 DB 구성도'의 값을 3개 행 입력
4. 입력이 완료되면 우측 하단의 [Apply] 버튼을 연속 클릭



#### 발생 가능한 오류
- 회원 테이블과 구매 테이블은 **기본 키(PK)-외래 키(FK) 관계**로 연결되어 있음 
- 따라서 구매 테이블의 `mem_id` 값은 반드시 회원 테이블의 `mem_id`로 존재해야 함
- 예를 들어, 회원 테이블에 등록되지 않은 `APN`을 구매자로 입력하면 오류가 발생 


#### 해결 방법
- Apply SQL Script to Database 창에서 [Cancel]을 눌러 종료
- 잘못 입력된 `APN` 행을 삭제
  - [Result Grid] 창에서 해당 행 선택 → 마우스 오른쪽 클릭 → **Delete Rows** 선택 → [Apply] 실행  
- 다시 [Apply], [Finish] 버튼을 클릭하여 나머지 데이터 입력을 완료
- 최종적으로 2건의 데이터가 정상 입력됨

외래 키 제약조건은 “구매자는 반드시 회원이어야 한다”는 의미

-> 회원가입 후에만 구매 가능


## 5. SQL로 테이블 만들기

### 5.1 회원 테이블 (기본 구조)


```sql
USE naver_db;

DROP TABLE IF EXISTS member;

CREATE TABLE member ( -- 회원 테이블
    mem_id CHAR(8),           -- 회원 아이디(PK)
    mem_name VARCHAR(10),     -- 이름
    mem_number TINYINT,       -- 인원수
    addr CHAR(2),             -- 주소 (예: 경기, 서울, 경남)
    phone1 CHAR(3),           -- 국번 (예: 02, 031, 055)
    phone2 CHAR(8),           -- 나머지 전화번호 (하이픈 제외)
    height TINYINT UNSIGNED,  -- 평균 키
    debut_date DATE           -- 데뷔 일자
);
```

USE naver_db; → naver_db 데이터베이스를 사용하겠다는 의미

DROP TABLE IF EXISTS member; → 기존에 member 테이블이 있다면 삭제

CREATE TABLE member (...) → 회원 테이블을 생성

열 이름과 데이터 형식만 지정 (PK, NOT NULL 등 옵션은 아직 미적용)

각 열은 콤마(,)로 구분하여 나열

### 5.2 NULL / NOT NULL 지정하기

테이블을 생성할 때, 각 열에 **NULL** 허용 여부를 지정 가능
- `NULL`: 빈 값을 허용 (값이 없어도 됨)  
- `NOT NULL`: 반드시 값을 입력해야 함  

아무것도 지정하지 않으면 기본값은 `NULL` 허용 
혼란을 줄이기 위해 모든 열에 대해 명시적으로 `NULL` 또는 `NOT NULL`을 지정하는 것이 좋음

---

#### ex) 회원 테이블 생성 (NULL/NOT NULL 지정)

```sql
DROP TABLE IF EXISTS member; -- 기존에 있으면 삭제

CREATE TABLE member (
    mem_id CHAR(8) NOT NULL,
    mem_name VARCHAR(10) NOT NULL,
    mem_number TINYINT NOT NULL,
    addr CHAR(2) NOT NULL,
    phone1 CHAR(3) NULL,
    phone2 CHAR(8) NULL,
    height TINYINT UNSIGNED NULL,
    debut_date DATE NULL
);
```

mem_id, mem_name, mem_number, addr → 반드시 입력해야 하므로 NOT NULL 지정

phone1, phone2, height, debut_date → 없어도 되므로 NULL 허용

DROP TABLE IF EXISTS 구문으로 기존 테이블 삭제 후 새로 생성

회원 아이디나 이름이 없는 회원은 있을 수 없으므로 NOT NULL로 지정


### 5.3 기본 키 설정

**기본 키(Primary Key, PK)** 는 각 행을 고유하게 구분할 수 있는 열
- 중복 불가  
- NULL 불가  
- 일반적으로 회원 아이디, 학번, 주민등록번호 등과 같이 고유해야 하는 값에 지정  

기본 키로 지정된 열은 `NOT NULL` 제약조건을 자동으로 가지므로 별도로 `NOT NULL`을 작성하지 않아도 됨

---

#### ex) 회원 테이블 생성 (기본 키 설정)

```sql
DROP TABLE IF EXISTS member; -- 기존에 있으면 삭제

CREATE TABLE member (
    mem_id CHAR(8) NOT NULL PRIMARY KEY, -- 기본 키 설정
    mem_name VARCHAR(10) NOT NULL,
    mem_number TINYINT NOT NULL,
    addr CHAR(2) NOT NULL,
    phone1 CHAR(3) NULL,
    phone2 CHAR(8) NULL,
    height TINYINT UNSIGNED NULL,
    debut_date DATE NULL
);
```

mem_id 열에 PRIMARY KEY 제약조건을 지정 → 각 회원을 유일하게 식별

기본 키가 지정된 열은 자동으로 NOT NULL 처리됨

회원 아이디(mem_id)는 반드시 존재해야 하고, 중복될 수 없음

네이버 회원이면서 아이디가 없는 경우는 불가능하므로 아이디를 기본 키로 지정

### 5.4 구매 테이블 생성하기

`buy` 테이블  

회원 테이블을 만들었던 것과 큰 차이는 없지만 `num` 열에는 **AUTO_INCREMENT** 속성을 설정  


#### ex) 구매 테이블 생성 (외래 키 제외)

```sql
DROP TABLE IF EXISTS buy; -- 기존에 있으면 삭제

CREATE TABLE buy ( -- 구매 테이블
    num INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 순번 (PK)
    mem_id CHAR(8) NOT NULL,                     -- 회원 아이디 (FK 예정)
    prod_name CHAR(6) NOT NULL,                  -- 제품 이름
    group_name CHAR(4) NULL,                     -- 분류
    price INT UNSIGNED NOT NULL,                 -- 가격
    amount SMALLINT UNSIGNED NOT NULL            -- 수량
);
```

1. num

구매 기록을 구분하기 위한 순번

AUTO_INCREMENT로 지정하여 값이 자동 증가

반드시 PRIMARY KEY 또는 UNIQUE 제약조건이 필요

2. mem_id

회원 아이디

추후 외래 키(FK)로 회원 테이블(member)과 연결 예정

3. price, amount

음수 값이 들어가지 않도록 UNSIGNED 지정

AUTO_INCREMENT는 보통 기본 키(PK) 열에 자주 사용되며, 데이터 입력 시 자동으로 1씩 증가

### 5.5 외래 키 설정

구매 테이블(`buy`)의 `mem_id` 열은 회원 테이블(`member`)의 `mem_id`와 연결되어야함 

-> 두 테이블 간의 관계를 정의하기 위해 **외래 키(Foreign Key, FK)** 제약조건을 사용 

외래 키는 “이 열의 값은 반드시 다른 테이블의 특정 열에 존재해야 한다”는 규칙을 의미 

-> 구매 기록은 반드시 회원 테이블에 존재하는 회원만 가질 수 있음 


#### ex) 구매 테이블 생성 (외래 키 포함)

```sql
DROP TABLE IF EXISTS buy; -- 기존에 있으면 삭제

CREATE TABLE buy (
    num INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 순번
    mem_id CHAR(8) NOT NULL,                     -- 회원 아이디 (FK)
    prod_name CHAR(6) NOT NULL,                  -- 제품 이름
    group_name CHAR(4) NULL,                     -- 분류
    price INT UNSIGNED NOT NULL,                 -- 가격
    amount SMALLINT UNSIGNED NOT NULL,           -- 수량
    FOREIGN KEY(mem_id) REFERENCES member(mem_id) -- 외래 키 지정
);
```

FOREIGN KEY(mem_id)

buy 테이블의 mem_id는 반드시 member 테이블의 mem_id에 존재해야 함

회원으로 등록되지 않은 사람은 구매 기록을 가질 수 없음

외래 키는 테이블 정의의 마지막에 추가하는 것이 일반적

외래 키 제약조건으로 인해 참조 무결성이 보장됨




<br>

---

# 2️⃣ 확인문제 & 추가학습

## 문제 1

> **🧚 온라인 쇼핑몰 DB를 설계하려 한다. 아래 스키마와 요구사항을 만족해야 한다.**
>
> - CATEGORY(CAT_ID, CAT_NAME)
> - PRODUCT(PROD_ID, PROD_NAME, PRICE, CAT_ID, STATUS)
> - 제약조건 요구:
>   1. CATEGORY.CAT_ID는 기본키
>   2. PRODUCT.PROD_ID는 기본키, PROD_NAME은 NULL 불가
>   3. PRODUCT.CAT_ID는 존재하는 카테고리만 허용(삭제 시 자식은 NULL 처리)
>   4. PRODUCT.PRICE는 0 이상
>   5. PRODUCT.STATUS는 'ACTIVE' 또는 'INACTIVE'만 허용
> - 운영팀 전용 뷰 V_ACTIVE_PRODUCT는 **활성 상품만** 조회하며, 뷰를 통해 INSERT/UPDATE 시에도 활성 상태만 들어오도록 강제해야 한다.
>
> **아래 보기 중 옳은 것을 모두 고르시오**

~~~sql
A. 
CREATE TABLE CATEGORY (
  CAT_ID   NUMBER      PRIMARY KEY,
  CAT_NAME VARCHAR(50) UNIQUE
);

B. 
CREATE TABLE PRODUCT (
  PROD_ID   NUMBER       PRIMARY KEY,
  PROD_NAME VARCHAR(100) NOT NULL,
  PRICE     NUMBER       CHECK (PRICE >= 0),
  CAT_ID    NUMBER,
  STATUS    VARCHAR(10)  CHECK (STATUS IN ('ACTIVE','INACTIVE')),
  CONSTRAINT FK_PROD_CAT
    FOREIGN KEY (CAT_ID) REFERENCES CATEGORY(CAT_ID)
      ON DELETE SET NULL
);

C. 
ALTER TABLE PRODUCT
  ADD CONSTRAINT UQ_PROD_NAME UNIQUE (PROD_NAME);
  
D. 
CREATE VIEW V_ACTIVE_PRODUCT AS
SELECT PROD_ID, PROD_NAME, PRICE, CAT_ID, STATUS
FROM PRODUCT
WHERE STATUS = 'ACTIVE'
WITH CHECK OPTION;

E. 정답은 없다. 왜냐하면 V_ACTIVE_PRODUCT는 집계가 없기 때문에 반드시 업데이트 불가능한 비갱신 뷰이다. 
~~~

옳은 것: B, D


A: CAT_ID PK는 맞지만, CAT_NAME UNIQUE는 요구사항에 없음

B: PK, NOT NULL, PRICE ≥ 0, STATUS 제약, FK + ON DELETE SET NULL → 요구사항 충족

C: PROD_NAME은 NOT NULL만 필요, UNIQUE 제약은 요구사항 아님 

D: 활성 상품만 조회, WITH CHECK OPTION으로 삽입/수정 시 ACTIVE만 가능

E: 단순 SELECT + WHERE 조건은 갱신 가능한 뷰 -> “정답 없음”은 틀림



## 추가학습 : 정규화

> **🧚. 다음 아래의 키워드를 참고하여 데이터 베이스의 정규화에 대한 개념을 더 공부하고 난 이후, 아래에 공부한 내용을 자유롭게 정리해주세요.**

~~~
데이터베이스 정규화
1NF
2NF
3NF
BCNF 
4NF, 5NF 는 선택으로 진행해주시면 됩니다. 

(각각의 차이에 집중하며 학습을 진행해주세요.)
~~~

# 데이터베이스 정규화

데이터베이스 설계 과정에서 중복을 최소화하고 데이터 무결성을 보장하기 위해 테이블을 구조적으로 나누는 과정

이상 현상(삽입, 삭제, 갱신 이상) 방지

일관성 있고 효율적인 데이터베이스 



## 1. 제1정규형 (1NF: First Normal Form)
- **조건**: 모든 속성값이 원자값(Atomic Value)만 가져야함  
- 한 칸(속성)에 여러 개의 값이 들어가면 안됨  
- 반복되는 그룹 제거, 다중값 컬럼 분리

ex)  
-  `전화번호 = "010-1234-5678, 010-9999-8888"` X
-  전화번호 테이블을 따로 분리하거나, 행(Row)으로 분리  


## 2. 제2정규형 (2NF: Second Normal Form)
- **조건**: 1NF 만족 + 부분 함수 종속 제거  
- 기본키가 복합키(2개 이상)일 경우, 키의 일부에만 종속되는 속성을 제거해야함 
- 모든 비키 속성은 기본키 전체에 종속되어야함 

ex) 
- `(학번, 과목코드) → 성적`, `과목명`은 과목코드에만 종속됨  
- 과목 정보를 별도 테이블로 분리 (`과목코드 → 과목명`)  


## 3. 제3정규형 (3NF: Third Normal Form)
- **조건**: 2NF 만족 + 이행 함수 종속 제거  
- 기본키가 아닌 다른 속성에 종속되는 속성이 없어야함  
- 모든 속성은 기본키에만 직접 종속되어야함

ex)
- `학번 → 학과코드 → 학과명`  
- `학번`으로 `학과코드`를 알 수 있고 `학과코드`로 `학과명`을 알 수 있음 → 이행 종속 발생  
- `학과코드, 학과명`을 분리해 학과 테이블로 관리  


## 4. BCNF (Boyce-Codd Normal Form)
- **조건**: 3NF보다 더 엄격한 형태  
- 모든 결정자(Determinant)는 후보키여야함
- 후보키가 아닌 속성이 다른 속성을 결정하는 경우를 제거

ex)
- `(교수, 과목) → 강의실`, `강의실 → 교수`
- 강의실이 후보키가 아니면서 결정자 역할
- 테이블을 더 분리하여 모든 결정자가 후보키가 되도록 수정  


## 5. 제4정규형 (4NF: Fourth Normal Form) 
- 다치 종속 제거  
- 한 테이블에 2개 이상의 독립적인 다치 종속 관계가 있으면 분리  


## 6. 제5정규형 (5NF: Fifth Normal Form)
- 조인 종속 제거  
- 불필요한 조인으로 인해 생길 수 있는 중복을 방지  



<br>

### 🎉 수고하셨습니다.
