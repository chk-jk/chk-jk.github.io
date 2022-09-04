---
title: "SQL Workbook DDL"
excerpt: "Data Definition Language"

categories:
  - study
tags:
  - SQL, Oracle
last_modified_at: 2022-09-03T12:00:00-05:00
---

# SQL WorkBook DDL

### 1번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled.png)

```sql
create table TB_CATEGORY(

    NAME VARCHAR2(10),
    USE_YN CHAR(1) Default 'Y'

);
```

### 2번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%201.png)

```sql
create table TB_CLASS_TYPE(
    
    NO VARCHAR(5) PRIMARY KEY,
    NAME VARCHAR2(10)
    
);
```

### 3번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%202.png)

```sql
ALTER TABLE TB_CATEGORY 
ADD CONSTRAINT C_NAME_PK PRIMARY KEY(NAME);
```

### 4번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%203.png)

```sql
ALTER TABLE TB_CLASS_TYPE
ADD CONSTRAINT CT_NAME_NN NOT NULL(NAME);
-- 오답

ALTER TABLE TB_CLASS_TYPE
MODIFY NAME NOT NULL;
```

### 5번문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%204.png)

```sql
ALTER TABLE TB_CLASS_TYPE
MODIFY(NO VARCHAR2(10), NAME VARCHAR2(20));

ALTER TABLE TB_CATEGORY
MODIFY(NAME VARCHAR2(20));
```

### 6번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%205.png)

```sql
ALTER TABLE TB_CLASS_TYPE 
RENAME COLUMN NO TO CLASS_TYPE_NO;
ALTER TABLE TB_CLASS_TYPE 
RENAME COLUMN NAME TO CLASS_TYPE_NAME; 
ALTER TABLE TB_CATEGORY
RENAME COLUMN NAME TO CATEGORY_NAME;
ALTER TABLE TB_CATEGORY
RENAME COLUMN USE_YN TO CATEGORY_USE_YN;
```

### 7번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%206.png)

```sql
ALTER TABLE TB_CATEGORY
RENAME CONSTRAINT C_NAME_PK TO PK_CATEGORY_NAME;
ALTER TABLE TB_CLASS_TYPE
RENAME CONSTRAINT SYS_C007135 TO PK_CLASS_TYPE_NO;
```

### 8번 문제

```sql
8. 다음과 같은 INSERT 문을 수행한다.

INSERT INTO TB_CATEGORY VALUES ('공학','Y');
INSERT INTO TB_CATEGORY VALUES ('자연과학','Y');
INSERT INTO TB_CATEGORY VALUES ('의학','Y');
INSERT INTO TB_CATEGORY VALUES ('예체능','Y');
INSERT INTO TB_CATEGORY VALUES ('인문사회','Y');
COMMIT;
```

### 9번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%207.png)

```sql
ALTER TABLE TB_DEPARTMENT 
ADD CONSTRAINT FK_DEPARTMENT_CATEGORY 
FOREIGN KEY (CATEGORY) REFERENCES TB_CATEGORY (CATEGORY_NAME);
```

### 10번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%208.png)

```sql
CREATE VIEW VW_학생일반정보
    AS SELECT   STUDENT_NO,
                STUDENT_NAME, 
                STUDENT_ADDRESS 
       FROM     TB_STUDENT;
```

### 11번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%209.png)

```sql
create view VW_지도면담
    as select student_name 학생이름,
              department_name 학과이름,
              professor_name 지도교수이름
        from  tb_student join tb_department using(department_no)
                         left join tb_professor 
                         on coach_professor_no = professor_no;
```

### 12번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%2010.png)

```sql
create view VW_학과별학생수
    as select   department_name 부서명,
                count(*) 학생수
       from     tb_student join tb_department using(department_no)
       group by department_name;
```

### 13번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%2011.png)

```sql
update  vw_학생일반정보
set     student_name = '송준경'
where   student_no = 'A213046';
```

### 14번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%2012.png)

```sql
CREATE VIEW VW_학생일반정보
    AS SELECT   STUDENT_NO,
                STUDENT_NAME, 
                STUDENT_ADDRESS 
       FROM     TB_STUDENT
       with read only;
```

### 15번 문제

![Untitled](/assets/images/sql_workbook_ddl/Untitled%2013.png)

```sql
select  *
from    (select  class_no 과목번호,
                 class_name 과목이름,
                 count(*) 누적수강생수(명)
         from    tb_class left join tb_grade using(class_no)
         where   substr(term_no, 1,4) between '2007' and '2009'
         group by class_no, class_name
         order by 3 desc)
where   rownum between 1 and 3;
```