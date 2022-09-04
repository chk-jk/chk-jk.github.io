---

title: "SQL Workbook DML"
excerpt: "Data Mauipulation Language"

categories: 
 - study

tags :
 - SQL
 - Oracle
last_modified_at: 2022-09-04T18:40:00-45:00

---
# SQL Workbook DML

### 1번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled.png)

```sql
insert into tb_class_type (class_type_no, class_type_name) values(01, '전공필수');
insert into tb_class_type (class_type_no, class_type_name) values(02, '전공선택');
insert into tb_class_type (class_type_no, class_type_name) values(03, '교양필수');
insert into tb_class_type (class_type_no, class_type_name) values(04, '교양선택');
insert into tb_class_type (class_type_no, class_type_name) values(05, '논문지도');
```

### 2번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%201.png)

```sql
create table TB_학생일반정보
as select student_no,
          student_name,
          student_address
   from   tb_student;
```

### 3번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%202.png)

```sql
create table TB_국어국문학과 AS (select  student_no 학번,
                                       student_name 학생이름,
                                       substr(student_ssn, 1, 2) + 1900 출생년도,
                                       professor_name 교수이름
                               from    tb_student left join tb_professor on coach_professor_no = professor_no
                               where   tb_student.department_no = (select tb_department.department_no 
                                                                   from tb_department 
                                                                   where department_name = '국어국문학과')           
                                );
```

### 4번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%203.png)

```sql
update tb_department 
set capacity = round(capacity + (capacity*0.1), 0)
where CAPACITY is not null;
```

### 5번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%204.png)

```sql
update tb_student 
set student_address = '서울시 종로구 승인동 181-21'
where student_no = 'A413042';
```

### 6번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%205.png)

```sql
update tb_student
set student_ssn = substr(student_ssn, 1, 6);
```

### 7번문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%206.png)

```sql
update  tb_grade
set     point = '3.5'
where   student_no = (select student_no 
                      from   tb_student
                      where  student_name = '김명훈' and
                             department_no = (select department_no 
                                              from tb_department
                                              where department_name = '의학과')
                      )
        and class_no = (select class_no 
                        from tb_class 
                        where class_name = '피부생리학')
        and term_no like '200501';

--- 확인

select  student_no 학번,
        (select department_name 
         from   tb_department 
         where  department_no = (select department_no 
                                 from tb_student 
                                 where student_no = 'A331101')
        ) 학과명,
        (select student_name 
         from   tb_student 
         where  student_no = 'A331101') 이름,
        point 학점
from    tb_grade
where   student_no = (select student_no 
                      from   tb_student
                      where  student_name = '김명훈' and
                             department_no = (select department_no 
                                              from tb_department
                                              where department_name = '의학과')
                      )
and class_no = (select class_no 
                from tb_class 
                where class_name = '피부생리학')
and term_no like '200501';
```

### 8번 문제

![Untitled](/assets/images/sql_workbook_dml/Untitled%207.png)

```sql
delete  
from    tb_grade
where   student_no in (select student_no 
                       from   tb_student 
                       where  absence_YN = 'Y');
```