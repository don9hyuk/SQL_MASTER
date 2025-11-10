# SQL_MASTER 6주차 정규 과제 

## Week 6 : 스토어드 프로시저

📌**SQL_MASTER 정규과제**는 매주 MySQL Workbench 툴을 활용하여 **직접 데이터베이스를 설계하고 실습하는 프로젝트 기반 과제**입니다. 

이번 주는 아래의 **SQL_MASTER_6th_TIL**에 나열된 주제를 중심으로 개념을 학습하고, 주차별 **학습 목표**에 맞게 정리해주세요. 정리한 내용은 GitHub에 업로드한 후, **스프레드시트의 'SQL' 시트에 링크를 제출**해주세요. 



> ✅ **과제 제출 시 반드시 “Workbench 실습 화면 (캡처)” 를 첨부해주세요!**
>
> - 테이블 생성, 수정, 삭제 명령이 실행된 화면 또는 결과가 확인되는 캡처를 첨부합니다.
> - Github에 정리하는 실습 TIL 링크를 **스프레드시트 'SQL_MASTER' 시트에 제출**해주세요.



## SQL_MASTER_6th_TIL

## 7장 : 스토어드 프로시저 

### 01. 스토어드 프로시저 사용 방법

### 02. 스토어드 함수와 커서 

### 03. 자동 실행되는 트리거





## 🏁 주차별 학습 (Study Schedule)

| 주차  | 공부 범위                  | 완료 여부 |
| ----- | -------------------------- | --------- |
| 1주차 | **데이터베이스와 SQL**     | ✅         |
| 2주차 | **실전용 SQL 미리 맛보기** | ✅         |
| 3주차 | **기본, 고급 DML 복습**    | ✅         |
| 4주차 | **테이블과 뷰**            | ✅         |
| 5주차 | **인덱스**                 | ✅         |
| 6주차 | **스토어드 프로시저**      | ✅         |
| 7주차 | **SQL 파이썬 연결하기**    | 🍽️         |

<br>

<!-- 여기까진 그대로 둬 주세요-->

---

# 1️⃣ 개념정리

## 01. 스토어드 프로시저

~~~
✅ 학습 목표 :
* 스토어드 프로시저의 작성 방법을 이해할 수 있다.
* 스토어드 함수와 커서에 대해서 이해하고 활용 방법을 학습할 수 있다.
* 트리거의 적용 분야와 실제 사용 방법을 배울 수 있다. 
~~~

### 7-1 스토어드 프로시저 사용 방법


**1. 개념 및 필요성**

스토어드 프로시저(Stored Procedure)
-  자주 사용하는 SQL문을 하나의 프로그램처럼 묶어 데이터베이스 내부에 저장하고 재사용하는 기능

- SQL 실행의 자동화, 효율성, 유지보수성을 높이며, 복잡한 연산을 간소화할 수 있음

- MySQL의 스토어드 프로시저는 프로그래밍 기능을 추가한 SQL 형태로, BEGIN ~ END 블록 내부에 여러 SQL문과 제어문을 포함할 수 있음

**2. 기본 구조**
```sql
DELIMITER $$
CREATE PROCEDURE 프로시저명([IN | OUT 매개변수])
BEGIN

END $$
DELIMITER ;
```

명령어	설명
~~~
DELIMITER	명령문, 구분자 변경 (기본 ‘;’ → ‘$$’ 등)
CREATE PROCEDURE	프로시저 생성
CALL	            프로시저 실행
DROP PROCEDURE	    프로시저 삭제

~~~

⚙️ 3. 기본 예시
```sql
USE market_db;
DROP PROCEDURE IF EXISTS user_proc;
DELIMITER $$
CREATE PROCEDURE user_proc()
BEGIN
   SELECT * FROM member;
END $$
DELIMITER ;
CALL user_proc();
```

기존 동일 이름의 프로시저가 있으면 삭제 후 재생성

member 테이블 전체 조회 수행

**4. 매개변수 사용**

(1) 입력 매개변수 (IN)
```sql
CREATE PROCEDURE user_proc1(IN userName VARCHAR(10))
BEGIN
   SELECT * FROM member WHERE mem_name = userName;
END $$
CALL user_proc1('에이핑크');
```

→ 외부에서 입력받은 이름으로 회원 정보 조회

(2) 출력 매개변수 (OUT)
```sql
CREATE PROCEDURE user_proc3(IN txtValue CHAR(10), OUT outValue INT)
BEGIN
   INSERT INTO noTable VALUES(NULL, txtValue);
   SELECT MAX(id) INTO outValue FROM noTable;
END $$
CALL user_proc3('테스트', @myValue);
SELECT @myValue;
```

→ 입력된 데이터를 테이블에 추가하고, 가장 큰 id 값을 반환

**5. 제어문 및 SQL 프로그래밍 활용**

조건문, 변수 선언 등 프로그래밍 로직 구현 가능

IF ~ ELSE, DECLARE, SELECT INTO 문 사용
```sql
CREATE PROCEDURE ifelse_proc(IN memName VARCHAR(10))
BEGIN
   DECLARE debutYear INT;
   SELECT YEAR(debut_date) INTO debutYear FROM member WHERE mem_name = memName;
   IF debutYear >= 2015 THEN
       SELECT '신인 가수예요.' AS 메시지;
   ELSE
       SELECT '고참 가수예요.' AS 메시지;
   END IF;
END $$
CALL ifelse_proc('아이린');
```

→ 데뷔 연도 기준으로 신인/고참 구분 메시지 출력



### 7-2. 스토어드 함수와 커서
**1. 스토어드 함수의 개념과 형식**

MySQL은 다양한 내장 함수를 제공

그러나 MySQL이 제공하지 않는 연산이 필요할 경우 사용자가 직접 함수를 정의할 수 있음

- 함수 기본 형식
```sql
DELIMITER $$
CREATE FUNCTION 스토어드_함수_이름(매개변수)
RETURNS 반환형식
BEGIN
    -- 이 부분에 프로그램 코딩 작성
    RETURN 반환값;
END $$
DELIMITER ;
```

RETURNS: 반환 데이터 타입을 지정

RETURN: 함수 결과값을 반환

함수는 단일 매개변수를 가질 수도 있고 여러 개를 받을 수도 있음

SELECT 문에서 직접 호출 가능 (SELECT 함수명();)

**2. 스토어드 함수 예제**
```sql
DROP FUNCTION IF EXISTS calcYearFunc;
DELIMITER $$
CREATE FUNCTION calcYearFunc(dYear INT)
RETURNS INT
BEGIN
    DECLARE runYear INT; -- 활동 기간(연도)
    SET runYear = YEAR(CURDATE()) - dYear;
    RETURN runYear;
END $$
DELIMITER ;

SELECT calcYearFunc(2010) AS '활동 연수';
```

🧾 결과:
```sql
활동 연수
11
```
→ 입력된 연도를 기준으로 현재 연도와의 차이를 계산해 “활동 연수”를 반환하는 함수

**3. 함수의 사용과 삭제**

함수 호출 시 SELECT문 안에서 사용

함수 삭제 시에는 DROP FUNCTION 명령어 사용
```sql
DROP FUNCTION calcYearFunc;
```

**4. 커서(Cursor)의 개념**

**커서**

- 데이터베이스에서 한 행씩 처리하기 위한 기능
- 대량의 데이터를 반복적으로 다룰 때 사용
- 하나의 SELECT 문 결과를 한 행씩 순차적으로 탐색할 수 있음

- 커서 사용 순서

        커서 선언하기

        반복 조건 선언하기 (HANDLER)

        커서 열기 (OPEN)

        반복문 실행 (LOOP)

        데이터 읽기 (FETCH)

        커서 닫기 (CLOSE)

**5. 커서 활용 예제 – 회원 평균 인원수 계산**

(1) 변수 선언하기
```sql
DECLARE memNumber INT;
DECLARE cnt INT DEFAULT 0;
DECLARE totNumber INT DEFAULT 0;
DECLARE endOfRow BOOLEAN DEFAULT FALSE;
```

memNumber: 각 회원의 인원수 저장

cnt, totNumber: 합계 계산용 변수

endOfRow: 커서 종료 조건 제어용 변수

(2) 커서 선언
```sql
DECLARE memberCursor CURSOR FOR
SELECT mem_number FROM member;
```

(3) 반복 조건 선언
```sql
DECLARE CONTINUE HANDLER
FOR NOT FOUND SET endOfRow = TRUE;
```

(4) 커서 열기
```sql
OPEN memberCursor;
```

(5) 반복문 실행
```sql
cursor_loop: LOOP
    FETCH memberCursor INTO memNumber;
    IF endOfRow THEN
        LEAVE cursor_loop;
    END IF;
    SET cnt = cnt + 1;
    SET totNumber = totNumber + memNumber;
END LOOP cursor_loop;
```

→ 한 행씩 데이터를 읽어 memNumber에 저장하고 누적 계산

(6) 커서 닫기
```sql
CLOSE memberCursor;
```

(7) 결과 계산
```sql
SELECT (totNumber / cnt) AS '회원의 평균 인원 수';
```

커서 통합 코드
```sql
DELIMITER $$
CREATE PROCEDURE cursor_proc()
BEGIN
    DECLARE memNumber INT;
    DECLARE cnt INT DEFAULT 0;
    DECLARE totNumber INT DEFAULT 0;
    DECLARE endOfRow BOOLEAN DEFAULT FALSE;

    DECLARE memberCursor CURSOR FOR
        SELECT mem_number FROM member;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET endOfRow = TRUE;

    OPEN memberCursor;
    cursor_loop: LOOP
        FETCH memberCursor INTO memNumber;
        IF endOfRow THEN
            LEAVE cursor_loop;
        END IF;
        SET cnt = cnt + 1;
        SET totNumber = totNumber + memNumber;
    END LOOP cursor_loop;
    CLOSE memberCursor;

    SELECT (totNumber / cnt) AS '회원의 평균 인원 수';
END $$
DELIMITER ;
```

### 7-3. 자동 실행되는 트리거

**트리거**

- INSERT, UPDATE, DELETE 문이 실행될 때 자동으로 작동하는 프로그램 코드

- 사용자가 직접 실행하지 않아도 테이블에서 특정 이벤트가 발생하면 트리거가 자동 실행되어 데이터 무결성을 유지

- 회사의 퇴사한 직원 데이터를 다른 테이블에 자동 저장하거나 삭제 내역을 별도 백업할 때 유용

- 트리거는 자동화된 데이터 검증 도구이며, 사람이 실수로 데이터를 누락하거나 삭제하는 것을 방지

**1. 트리거의 개념과 기본 구조**

트리거는 데이터베이스 테이블에 이벤트 기반으로 자동 실행되는 코드를 부착하는 개념

특정 테이블에 대해 INSERT, UPDATE, DELETE가 일어날 때 실행

- 트리거 기본 문법
```sql
DELIMITER $$
CREATE TRIGGER 트리거명
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON 테이블명
FOR EACH ROW
BEGIN
    -- 트리거 실행 코드
END $$
DELIMITER ;
```


```sql
BEFORE / AFTER	: 트리거 실행 시점 지정
INSERT / UPDATE / DELETE  :	트리거가 반응할 이벤트 유형
FOR EACH ROW : 영향을 받는 각 행마다 트리거 실행
```

**2. 트리거 기본 동작**

- 트리거는 DML 명령 (INSERT, UPDATE, DELETE)이 수행될 때 자동으로 호출

- 일반적으로 BEFORE 트리거는 실행 전 검사나 수정에, AFTER 트리거는 로그 기록이나 백업에 사용됨

**3. 트리거 실습 예제**
```sql
USE market_db;

CREATE TABLE IF NOT EXISTS trigger_table (
    id INT,
    txt VARCHAR(10)
);

INSERT INTO trigger_table VALUES(1, '실험1');
INSERT INTO trigger_table VALUES(2, '실험2');
INSERT INTO trigger_table VALUES(3, '실험3');
```

- 트리거 생성
```sql
DROP TRIGGER IF EXISTS myTrigger;
DELIMITER $$
CREATE TRIGGER myTrigger
AFTER DELETE
ON trigger_table
FOR EACH ROW
BEGIN
    SET @msg = '가수 그룹이 삭제됨';
END $$
DELIMITER ;
```

```sql
AFTER DELETE	삭제 후 자동 실행됨
@msg	트리거 실행 시 메시지 저장용 변수
FOR EACH ROW	삭제된 각 행마다 동작
🔄 트리거 실행 확인
SET @msg = '';
INSERT INTO trigger_table VALUES(4, '테스트');
SELECT @msg;

UPDATE trigger_table SET txt='변경' WHERE id=3;
SELECT @msg;

DELETE FROM trigger_table WHERE id=4;
SELECT @msg;
```

🧾 결과 예시
```sql
INSERT	@msg 값 없음
UPDATE	@msg 값 없음
DELETE	@msg = '가수 그룹이 삭제됨'
```

-> DELETE문 실행 시 트리거가 작동하여 @msg 변수에 메시지를 자동 기록

💡 4. 트리거의 활용

- 트리거는 데이터 변경 이력 관리에 주로 사용

- 회원 정보가 수정되거나 삭제될 때마다 변경된 내용을 백업 테이블에 자동 기록할 수 있음
```sql
USE market_db;
CREATE TABLE singer
SELECT mem_id, mem_name, mem_number, addr
FROM member;
```

백업을 위한 테이블 생성:
```sql
CREATE TABLE backup_singer (
    mem_id CHAR(8) NOT NULL,
    mem_name VARCHAR(10) NOT NULL,
    mem_number INT NOT NULL,
    addr CHAR(2) NOT NULL,
    modType CHAR(2),     -- 변경 타입 ('수정' or '삭제')
    modDate DATE,        -- 변경 일자
    modUser VARCHAR(30)  -- 변경 사용자
);
```

UPDATE용 트리거 생성
```sql
DROP TRIGGER IF EXISTS singer_updateTrg;
DELIMITER $$
CREATE TRIGGER singer_updateTrg
AFTER UPDATE
ON singer
FOR EACH ROW
BEGIN
    INSERT INTO backup_singer
    VALUES (OLD.mem_id, OLD.mem_name, OLD.mem_number, OLD.addr,
            '수정', CURDATE(), CURRENT_USER());
END $$
DELIMITER ;
```

→ OLD는 변경 전 데이터를 의미하며 수정 전의 상태를 backup_singer에 저장

DELETE용 트리거 생성
```sql
DROP TRIGGER IF EXISTS singer_deleteTrg;
DELIMITER $$
CREATE TRIGGER singer_deleteTrg
AFTER DELETE
ON singer
FOR EACH ROW
BEGIN
    INSERT INTO backup_singer
    VALUES (OLD.mem_id, OLD.mem_name, OLD.mem_number, OLD.addr,
            '삭제', CURDATE(), CURRENT_USER());
END $$
DELIMITER ;
```

데이터 변경 테스트
```sql
UPDATE singer SET addr='경기' WHERE mem_id='BLK';
DELETE FROM singer WHERE mem_number >= 7;
SELECT * FROM backup_singer;
```

결과 예시
```sql
mem_id	mem_name	mem_number	addr	modType	modDate	modUser
BLK	블락비	4	경기	수정	2021-07-25	root@localhost
GRL	소녀시대	8	서울	삭제	2021-07-25	root@localhost
OMY	오마이걸	6	서울	삭제	2021-07-25	root@localhost
```

**5. TRUNCATE와 트리거의 관계**
```sql
TRUNCATE TABLE singer;
```

- TRUNCATE 명령은 테이블의 모든 데이터를 삭제하지만
트리거가 작동하지 않음

- 트리거는 DELETE, INSERT, UPDATE 명령에만 반응

**6. OLD / NEW 테이블 이해**

- 트리거는 내부적으로 임시 테이블을 사용해 이전 행과 새로운 행을 구분

```sql
OLD: 변경 전 데이터
NEW: 변경 후 데이터
```
작동 방식

INSERT → NEW에 새 데이터 저장

DELETE → OLD에 삭제된 데이터 저장

UPDATE → OLD(수정 전) / NEW(수정 후) 둘 다 존재



<br>

---

# 2️⃣ 확인문제 & 추가학습

## 문제

> **🧚쿠팡과 같은 쇼핑몰에서 회원이 상품을 결제하면, 장바구니에 담긴 상품 수량은 자동으로 차감되어야 합니다. 아래와 같은 테이블이 있다고 가정합니다.**

| **테이블명** | **컬럼명**                    | **설명**                  |
| ------------ | ----------------------------- | ------------------------- |
| Cart         | cart_id, product_id, quantity | 장바구니 정보 (수량 포함) |
| Product      | product_id, stock_quantity    | 상품 재고 수량            |

> **장바구니에 담긴 상품이 결제될 때, Product 테이블의 재고(stock_quantity)가 자동으로 감소하도록 트리거를 작성하세요.**
>
> > **(Cart의 quantity만큼 Product의 stock_quantity가 줄어듭니다.)**



~~~
DROP TRIGGER IF EXISTS trg_reduce_stock;
DELIMITER $$

CREATE TRIGGER trg_reduce_stock
AFTER DELETE
ON Cart
FOR EACH ROW
BEGIN
    UPDATE Product
    SET stock_quantity = stock_quantity - OLD.quantity
    WHERE product_id = OLD.product_id;
END $$

DELIMITER ;



AFTER DELETE
결제 완료 후, 장바구니에서 상품이 삭제된 뒤 트리거 실행

OLD.quantity
결제된 상품의 장바구니 수량 참조

OLD.product_id
결제된 상품의 ID 참조

UPDATE Product ...	
상품 테이블의 재고를 감소시킴
~~~



<br>

### 🎉 수고하셨습니다.
