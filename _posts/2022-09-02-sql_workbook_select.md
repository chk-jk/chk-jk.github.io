---
title: "SQL Workbook"
excerpt: "Select문 과제"

categories:
  - homework, study
tags:
  - SQL, ORACLE
last_modified_at: 2022-09-02T15:30:00-35:00
---

# join 과 subquery 를 이용한 쿼리 문제 (SELECT)

### 220825, 220902


## 기본 SELECT 문

### 1번 문제

> 춘 기술대학교의 학과 이름과 계열을 표시하시오. 단, 출력 헤더는 "학과 명", "계열"으로 표시하도록 한다.
> 

```sql
SELECT department_name 학과이름,
       category        계열
FROM   tb_department
ORDER BY 계열, 학과이름;
```

### 2번문제

![Untitled](/assets/images/sql_workbook_select/Untitled.png)

```sql
select  department_name || '의 정원은 ' || 
        capacity || '명 입니다.' "학과별 정원"
from    tb_department
order by department_name;
```

### 3번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%201.png)

```sql
select  student_name
from    tb_student
where   ABSENCE_YN = 'Y' 
        and department_no = 001
        and student_ssn like '%-2%';
```

### 4번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%202.png)

```sql
select  student_name "도서 장기 연체자"
from    tb_student
where   student_no
        in ('A513079', 'A513090', 'A513091', 'A513110', 'A513119')
order by "도서 장기 연체자" desc;
```

### 5번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%203.png)

```sql
select  department_name, category
from    tb_department
where   CAPACITY between 20 and 30;
```

### 6번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%204.png)

```sql
select  professor_name
from    tb_professor
where   department_no is null;
```

### 7번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%205.png)

```sql
select  student_name
from    tb_student
where   department_no is null 
        or not department_no between 1 and 63;
-- 하나만 써도 되나
```

### 8번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%206.png)

```sql
-- 선수과목이란? 선 수강과목
select  class_no
from    tb_class
where   preattending_class_no is not null;
```

### 9번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%207.png)

```sql
select distinct category -- 중복 제거
from tb_department;
```

### 10번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%208.png)

```sql
select  student_no, student_name, student_ssn
from    tb_student
where   student_address like '%전주시%' 
        and Entrance_date like '02%'
        and ABSENCE_YN = 'N'
order by student_name;
```

## Additional select - 함수

### 1번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%209.png)

```sql
select  student_no 학번,
        student_name 이름,
        to_char(entrance_date, 'yyyy-mm-dd') 입학년도
from    tb_student
where   DEPARTMENT_NO = 002
order by 입학년도;
```

### 2번문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2010.png)

```sql
select  professor_name,
        professor_ssn
from    tb_professor
where   length(professor_name) != 3;
```

### 3번문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2011.png)

```sql

select  professor_name 교수이름,
        floor((sysdate - to_date('19'||(substr(professor_ssn,1,6)), 'yyyy-mm-dd'))/365) 나이
from    tb_professor
order by 나이;
```

### 4번문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2012.png)

```sql
select substr(professor_name, 2, length(professor_name)) 이름
from tb_professor
order by 이름;
```

### 5번 문제 (미완)

![Untitled](/assets/images/sql_workbook_select/Untitled%2013.png)

```sql
물어보기 'RRRR' 의 개념을 잘 모르겠다.
```

### 6번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2014.png)

```sql
select to_char(to_date('20201225', 'yyyymmdd'), 'yyyymmdd dy') from dual;
```

### 7번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2015.png)

```sql
select  to_char(to_date('99/10/11', 'yy/mm/dd'), 'yyyy') "1",
        to_char(to_date('49/10/11', 'yy/mm/dd'), 'yyyy') "2",
        to_char(to_date('99/10/11', 'rr/mm/dd'), 'rrrr') "3",
        to_char(to_date('49/10/11', 'yy/mm/dd'), 'rrrr') "4"
from    dual;
```

![Untitled](/assets/images/sql_workbook_select/Untitled%2016.png)

### 8번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2017.png)

```sql
select  student_no, student_name
from    tb_student
where   student_no not like '%A%'
order by 1;
```

### 9번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2018.png)

```sql
select round(avg(point),1) 평점 from tb_grade group by STUDENT_NO having student_no = 'A517178';
```

### 10번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2019.png)

```sql
select department_no 학과번호 , count(*) "학생수(명)" from tb_student group by department_no order by 1;
```

### 11번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2020.png)

```sql
select  count(*) from tb_student where coach_professor_no is null;
```

### 12번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2021.png)

```sql
SELECT Substr(term_no, 1, 4) 년도,
       Round(Avg(point), 1)  "년도 별 평점"
FROM   tb_grade
GROUP  BY Substr(term_no, 1, 4),
          student_no
HAVING student_no = 'A112113';
```

### 13번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2022.png)

```sql
select  department_no 학과코드, 
        count(case 
                    when ABSENCE_YN='Y' then 1
                    else null
              end) 휴학생
from    tb_student 
group by department_no 
order by 1;
```

> `-- COUNT(*)와 GROUP BY를 같이 사용하는 경우 COUNT의 결과가 0인 행이 조회되지 않는다.`
> 

### 14번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2023.png)

```sql
SELECT student_name,
       Count(*)
FROM   tb_student
GROUP  BY student_name
HAVING Count(student_name) > 1;
```

### 15번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2024.png)

```sql
select * from tb_grade;
select  nvl(substr(term_no, 1, 4), ' ') 년도, 
        nvl(substr(term_no, 5, 2), ' ') 학기,
        round(avg(point), 1) 평점
from    TB_GRADE
where student_no = 'A112113'
group by rollup(substr(term_no, 1, 4), substr(term_no, 5, 2))
```
> 집계함수 → `rollup`, `cube` (group by)
> 

> `rollup` 사용
> 

# additional select - option

### 1번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2025.png)

```sql
select student_name "학생 이름", student_ADDress 주소지 from tb_student order by 1;
```

### 2번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2026.png)

```sql
select student_name,
        student_ssn
from tb_student
where ABSENCE_YN = 'Y'
order by 2 desc;
```

### 3번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2027.png)

```sql
select  student_name 학생이름,
        student_no 학번,
        student_address "거주지 주소"
from    tb_student
where   substr(student_no, 1, 1) like '9%'
        and substr(student_address, 1, 3) in ('강원도','경기도')
order by 1;
```

### 4번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2028.png)

```sql
select professor_name,
        professor_ssn
from tb_professor
where department_no = (select department_no
                        from TB_DEPARTMENT
                        where department_name = '법학과')
order by 2;
```

### 5번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2029.png)

```sql
select  student_no,
        point
from    tb_student join TB_GRADE using(student_no)
where   substr(term_no, 3,4) = '0402'
        and class_no = 'C3118100'
order by 2 desc;
```

### 6번문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2030.png)

```sql
select  student_no,
        student_name,
        department_name
from    tb_student left join tb_department using(department_no)
order by 1;
```

### 7번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2031.png)

```sql
select  class_name,
        department_name
from    tb_class left join tb_department using(department_no);
```

### 8번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2032.png)

```sql
select  class_name,
        nvl(professor_name, '담당교수 없음')
from    tb_class left join TB_class_professor using (class_no)
                 left join tb_professor using (professor_no);
```

### 9번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2033.png)

```sql
select  class_name,
        professor_name
from    tb_class_professor join tb_professor
                           using (professor_no)
                           join tb_department
                           using (department_no)
                           join tb_class
                           using (CLASS_NO)
where category like '%인문사회%';
```



### 10번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2034.png)

```sql
select  student_no 학번,
        student_name "학생 이름",
        round(avg(point),1) "전체 평점"
from    tb_student left join tb_grade using(student_no)
where department_no = (select department_no from tb_department where department_name = '음악학과')
group by student_no, student_name
order by 1;
```

### 11번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2035.png)

```sql
select  department_name 학과이름,
        student_name 학생이름,
        professor_name 지도교수이름
from    tb_student left join tb_department using(department_no)
                    left join tb_professor on professor_no = coach_professor_no
where   student_no = 'A313047';
```

### 12번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2036.png)

```sql
select  student_name,
        term_no
from    tb_student left join tb_grade using (student_no)
                    left join tb_class using (class_no)
where   term_no like '2007%' and
        class_name like '인간관계론';
```

### 13번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2037.png)

```sql
SELECT class_name,
       department_name
FROM   tb_class
       JOIN tb_department using (department_no)
       LEFT JOIN tb_professor using (department_no)
WHERE  category = '예체능'
       AND professor_name IS NULL;

--오답인것 같다.

-- 정답
SELECT class_name,
       department_name
FROM   tb_class
       JOIN tb_department using (department_no)
       left join tb_class_professor using(class_no)
WHERE  category = '예체능'
       AND professor_no IS NULL;
```

### 14번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2038.png)

```sql
select  student_name 학생이름,
        nvl(professor_name, '지도교수 미지정') 지도교수 
from    tb_student 
        left join (select professor_no, 
                          professor_name 
                   from tb_professor) 
        on coach_professor_no = professor_no
where department_no = (select department_no 
                       from tb_department 
                       where department_name = '서반아어학과')
order by tb_student.STUDENT_NO;
```

### 15번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2039.png)

```sql
select  student_no 학번,
        student_name 이름,
        department_name "학과 이름",
        round(avg(point), 2) 평점
from    tb_student join tb_department using(department_no)
                   join tb_grade using (student_no)
where   absence_yn = 'N'
group by student_no, student_name, department_name
having  avg(point) >= 4.0
order by 1;
```

### 16번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2040.png)

```sql
select  class_no,
        class_name,
        avg(point)
from    tb_class left join tb_grade using(class_no)
                left join tb_department using(department_no)
where   class_type like '전공%' 
        and department_name = '환경조경학과'
group by class_no, class_name
order by 1;
```

### 17번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2041.png)

```sql
select  student_name,
        student_address
from    tb_student
where   department_no = (select department_no 
                         from   tb_student 
                         where  student_name = '최경희');
```

### 18번문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2042.png)

```sql
select   student_no, 
         student_name
from    (select student_no,
                student_name,
                avg(point)
         from   tb_student left join tb_grade using(student_no)
         where  department_no = (select department_no 
                                 from   tb_department 
                                 where  department_name = '국어국문학과')
         group by student_no, student_name
         order by 3 desc)
where rownum = 1;
```

### 19번 문제

![Untitled](/assets/images/sql_workbook_select/Untitled%2043.png)

```sql
select  department_name "계열 학과명",
        round(avg(point),1)
from    tb_department join tb_class using(department_no)
                      left join tb_grade using(class_no)
where   category = (select category 
                    from   TB_DEPARTMENT 
                    where  department_name = '환경조경학과')
group by department_name
order by 1;
```