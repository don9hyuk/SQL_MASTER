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

<!-- 새롭게 배운 내용을 자유롭게 정리해주세요.-->





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

<!-- 정답이 1개가 아닐수도 있습니다. -->



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



<br>

### 🎉 수고하셨습니다.
