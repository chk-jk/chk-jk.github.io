---
title: "SQL 활용 평가"
excerpt: "기록용"

categories:
  - test
tags:
  - SQL
  - ORACLE
last_modified_at: 2022-09-02T15:30:00-35:00
---

# SQL 활용 평가

# 서술형

### 데이터 딕셔너리

- 데이터 사전은 오라클에서 유저가 입력한 데이터를 바탕으로 만들어진 객체에 대한 데이터를 담고있는 데이터이다.
- 객체에 대한 데이터란 테이블명, 컬럼명, 개체 속성, 제약조건 혹은 유저가 직접 작성하지 않더라도 디폴트로 설정되는 값들이 저장되어
- 유저가 그 정보를 바탕으로 객체 관리를 더욱 용이하게 도와준다.

### TCL 3가지 명령어

commit

- 현재 실행중인 트랜잭션을 완료 후 저장함.

rollback

- 가장 최근에 트랜잭션이 완료된 시점으로 다시 복구하는 작업.

savepoint

- rollback 의 분기점을 생성하여 rollback 시 savepoint 명령을 완료한 시점으로 복구가 이루어지게 하는 분기점 생성 명령

### alter

- alter는 데이터 정의어 (DDL) 중의 하나로 객체의 상태를 수정 하는 명령어이다.

> 예시로 데이터의 속성을 변경하는데는 DML인
UPDATE 명령어를 사용하지만 UPDATE 명령어로는 컬럼의 제약 조건을 변경하거나, 테이블명을 변경하는 식의 객체의 속성을 변경 할 수는 없다. 이런 상황의 경우 Alter 명령어를 통해 객체의 상태를 수정할 수 있다.
> 

---

# 문제 해결 시나리오

## 원인

## 요구사항과 맞지 않는 쿼리

> **Product 테이블의 Product_name 컬럼의 데이터타입의 길이가 요구사항과 다름.**

요구사항에서는 varchar2(20) 을 요구하지만, 실제 작성된 쿼리는

작성된 쿼리 : `PRODUCT_NAME VARCHAR2(50) NOT NULL,`

문자열의 길이를 50으로 설정하였습니다.

> **Product 테이블의 serial_no 의 제약 조건이 빠져있음.**

시리얼 넘버는 각 상품의 고유번호이므로 중복되어선 안됩니다.

요구사항에서도 Unique key (고유키) 설정을 요구하지만

실제 쿼리에서는 작성되지 않았습니다.

작성된 쿼리 : `SERIAL_NO VARCHAR2(100)`

> **브랜드 번호 시퀀스 최댓값 상이**

시퀀스 요구 사항 : `SEQ_BRAND_ID` : 100부터 시작하여 100씩 증가, 최대 값 1000, 반복 없음.

최대 값 1000을 요구하지만 실제 작성된 쿼리는

```sql
-- SEQ_BRAND_ID 시퀀스 생성

CREATE SEQUENCE SEQ_BRAND_ID

START WITH 100

INCREMENT BY 100

MAXVALUE 500 <<

NOCYCLE;
```

요구사항과 상이한 부분이 적혀있습니다.

> **PK 에 중복된 값을 부여하려 함.**

- BRANDS 테이블 데이터 삽입에 사용된 SQL 구문이

```sql
INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.NEXTVAL, '삼성');

INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.CURRVAL, '애플');
```

총 두 가지 있는데 위에 쿼리를 실행하게 되면,

두 개의 중복되지 아니한 값을 가져야 하는 PK인 BRAND_ID 가 같은 값을 가지게 되므로

`Unique constraints violation` 에러가 나게 되어 시퀀싱이 제대로 이루어지지 않게 됩니다.

## 조치내용

> Product 테이블의 Product_name 컬럼의 데이터 타입의 길이가 요구사항과 다름.
> 

```sql
작성된 쿼리 : PRODUCT_NAME VARCHAR2(50) NOT NULL,

수정한 쿼리 : PRODUCT_NAME VARCHAR2(20) NOT NULL,

자료형의 길이를 요구사항에 맞추어 작성
```

> Product 테이블의 serial_no 의 제약조건이 빠져있음.
> 

```sql
작성된 쿼리 : SERIAL_NO VARCHAR2(100)

수정한 쿼리 : SERIAL_NO VARCHAR2(100) UNIQUE

요구사항에 맞추어 UK 제약조건을 추가했습니다.
```

> 브랜드 번호 시퀀스 최댓값 상이
> 

작성된 쿼리

```sql
CREATE SEQUENCE SEQ_BRAND_ID

START WITH 100

INCREMENT BY 100

MAXVALUE 500 <<<

NOCYCLE;

표시한 부분을 요구사항에 맞추어

MAXVALUE 1000

로 작성해주었습니다.
```

> PK 에 중복된 값을 부여하려 함.
> 

사용된 쿼리

```sql
INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.NEXTVAL, '삼성');

INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.CURRVAL, '애플');
```

중복된 값이 삽입되지 아니하도록

수정한 쿼리

```sql
INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.NEXTVAL, '애플');
```

`CURRVAL`을 `NEXTVAL`로 바꾸어주었습니다.

> --SQL---
> 

```sql
drop table products; -- 순서대로 해줄 것

drop table brands;

drop sequence seq_brand_ID;

drop sequence seq_product_no;

CREATE TABLE BRANDS(

BRAND_ID NUMBER PRIMARY KEY,

BRAND_NAME VARCHAR2(100) NOT NULL

);

CREATE TABLE PRODUCTS(

PRODUCT_NO NUMBER PRIMARY KEY,

PRODUCT_NAME VARCHAR2(20) NOT NULL, -- varchar2 20

PRODUCT_PRICE NUMBER NOT NULL,

BRAND_CODE NUMBER REFERENCES BRANDS,

SERIAL_NO VARCHAR2(100) unique, -- unique 값 부여

SOLD_OUT CHAR(1) DEFAULT 'N' CHECK(SOLD_OUT IN ('Y', 'N'))

);

CREATE SEQUENCE SEQ_BRAND_ID

START WITH 100

INCREMENT BY 100

MAXVALUE 1000 -- 최댓값 500 to 1000

NOCYCLE;

CREATE SEQUENCE SEQ_PRODUCT_NO

START WITH 1

INCREMENT BY 1

MAXVALUE 10000

NOCYCLE;

INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.NEXTVAL, '삼성');

INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.NEXTVAL, '애플'); -- 쿼리 수정

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '갤럭시S8', 800000, 100, 'S8','Y');

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '갤럭시S9', 900000, 100, 'S9','N');

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '갤럭시S10', 1000000, 100, 'S10','N');

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '아이폰9S', 900000, 200, '9S','N');

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '아이폰10S', 1000000, 200, '10S','N');

SELECT PRODUCT_NAME, PRODUCT_PRICE, BRAND_NAME, SOLD_OUT

FROM PRODUCTS JOIN BRANDS ON (BRAND_ID = BRAND_CODE);

- --ALTER---

alter table products add constraint sn_uk unique(serial_no);

alter table products modify product_name VARCHAR2(20);

```
---


# 모범답안

> 데이터 딕셔너리에 대하여 서술

- 자원을 효율적으로 관리하기 위한 다양한 정보를 저장하는 시스템 테이블.
- 데이터 딕셔너리는 사용자가 테이블을 생성하거나 사용자를 변경하는 등의 작업을 진행할 때 데이터베이스 서버에 의해 자동으로 갱신되는 테이블.

> **TCL (Transaction Control Language)에 해당되는 구문들을 3가지 적고 설명하시오**

- COMMIT : 메모리 버퍼에 임시 저장된 데이터를 DB에 반영
- ROLLBACK : 메모리 버퍼에 임시 저당된 데이터를 삭제하고 마지막 COMMIT 상태로 돌아감
- SAVEPOINT : 저장지점을 정의하면 롤백 할 때 트랜잭션에 포함된 전체 작업을 롤백하는 것이 아닌, 현 시점에서 지정한 SAVEPOINT 까지 트랜잭션 일부만 롤백

> **SQL 구문 중 'ALTER' 구문에 대하여 서술하시오**

테이블에 정의된 내용을 수정 할 때 사용하는 데이터 정의어


> ### 문제해결 시나리오
```sql
1) PRODUCTS 테이블의 SERIAL_NO 컬럼을 테이블 정의서 조건에 맞게 UNIQUE 제약조건 추가.
A
LTER TABLE PRODUCTS ADD UNIQUE(SERIAL_NO);

2) PRODUCTS 테이블의 PRODUCT_NAME 컬럼을 테이블 정의서 조건에 맞게 수정

ALTER TABLE PRODUCTS MODIFY PRODUCT_NAME VARCHAR2(20);

2) SEQ_BRAND_ID 시퀀스의 MAXVALUE 값을 요구사항에 맞게 1000으로 수정.

ALTER SEQUENCE SEQ_BRAND_ID MAXVALUE 1000;

3) '애플' 브랜드 데이터 삽입 구문의 CURRVAL을 NEXTVAL로 수정하여 INSERT 수행.

INSERT INTO BRANDS VALUES (SEQ_BRAND_ID.NEXTVAL, '애플');

4) 삽입 동작이 진행되지 않았던 '아이폰9S', '아이폰10S' INSERT 구문을 다시 수행.

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '아이폰9S', 900000, 200, '9S','N');

INSERT INTO PRODUCTS VALUES (SEQ_PRODUCT_NO.NEXTVAL, '아이폰10S', 1000000, 200, '10S','N');
```
