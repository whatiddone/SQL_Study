# 2주차

## 📖 복합 JOIN & GROUP BY + HAVING 학습

>## ✅ 복합 JOIN 학습

📖 **공식 문서 참고**: 🔗 [MySQL 공식 문서 - 조인](https://dev.mysql.com/doc/refman/8.0/en/join.html)

### 15.2.13.2 JOIN Clause
A table reference is also known as a join expression.

MySQL, JOIN, CROSS JOIN, and INNER JOIN are syntactic equivalents. In standard SQL, they are not equivalent. INNER JOIN is used with an ON clause, CROSS JOIN is used otherwise.

- MySQL JOIN Syntax Structure
![image](2025_Advanced/image/2nd_week/1.png)
```
table_references는 전체 JOIN 구문의 시작점

escaped_table_reference를 통해 ODBC 호환 가능성 포함
*ODBC: 다양한 DB에 연결할 수 있는 표준 인터페이스

table_reference는 기본 테이블 또는 JOIN된 테이블로 분기

table_factor는 실제 테이블, 서브쿼리, 괄호로 묶인 참조 등

joined_table은 다양한 JOIN 형태(INNER, LEFT, NATURAL 등)와 조인 조건 포함
```

#### 📘 JOIN 종류 요약

| JOIN 종류           | 키워드 예시                                 | 설명 |
|--------------------|---------------------------------------------|------|
| **INNER JOIN**     | `INNER JOIN`, `JOIN`                        | 두 테이블에서 **조건을 만족하는** 행만 반환합니다. 기본 JOIN입니다. |
| **LEFT JOIN**      | `LEFT JOIN`, `LEFT OUTER JOIN`             | 왼쪽 테이블의 **모든 행과** 조건을 만족하는 오른쪽 테이블의 행을 반환합니다. 일치하지 않으면 `NULL`로 채웁니다. |
| **RIGHT JOIN**     | `RIGHT JOIN`, `RIGHT OUTER JOIN`           | 오른쪽 테이블의 **모든 행과** 조건을 만족하는 왼쪽 테이블의 행을 반환합니다. 일치하지 않으면 `NULL`로 채웁니다. |
| **CROSS JOIN**     | `CROSS JOIN`                                | 두 테이블의 **모든 행 조합**(카테시안 곱)을 반환합니다. 조인 조건이 없습니다. |
| **NATURAL JOIN**   | `NATURAL JOIN`, `NATURAL LEFT JOIN` 등     | 두 테이블에서 **동일한 이름의 컬럼들**을 자동으로 기준 삼아 조인합니다. `ON` 또는 `USING` 절이 필요 없습니다. |
| **STRAIGHT_JOIN**  | `STRAIGHT_JOIN`                             | `INNER JOIN`과 유사하지만, MySQL 옵티마이저가 **조인 순서를 변경하지 못하도록 고정**합니다. |
| **ODBC JOIN**      | `{ OJ ... LEFT OUTER JOIN ... }`           | ODBC 호환을 위한 **특수한 OUTER JOIN 문법**입니다. MySQL에서는 일반 `LEFT JOIN`처럼 처리됩니다. |

#### Nested Join(중첩 조인)
- 조인 안에 또 다른 조인을 넣는 구조
```sql
SELECT *
FROM (A JOIN B ON A.id = B.a_id) # 괄호를 사용해 조인의 우선순위를 명시적으로 지정
JOIN C ON B.id = C.b_id;
```
- 사용하는 이유
```
1. 복잡한 조인 조건을 단계별로 나누기 위해
2. JOIN 순서를 명확히 하기 위해
3. MySQL 옵티마이저가 JOIN 순서를 바꾸지 않게 하기 위해
4. VIEW 또는 서브쿼리처럼 조인 결과를 잠깐 저장해 활용하기 위해
``` 

>## 🔎 문제 풀이
### 🔗 [programmers - 저자별 카테고리 별 매출액 집계하기](https://school.programmers.co.kr/learn/courses/30/lessons/144856)

```sql
SELECT
    B.AUTHOR_ID,
    A.AUTHOR_NAME,
    B.CATEGORY,
    SUM(S.SALES*B.PRICE) AS TOTAL_SALES
FROM BOOK B
JOIN AUTHOR A ON B.AUTHOR_ID = A.AUTHOR_ID
JOIN BOOK_SALES S ON B.BOOK_ID = S.BOOK_ID
WHERE YEAR(S.SALES_DATE) = 2022
    AND MONTH(S.SALES_DATE) = 1
GROUP BY B.AUTHOR_ID, B.CATEGORY
ORDER BY AUTHOR_ID ASC, CATEGORY DESC
```
![image](2025_Advanced/image/2nd_week/2.png)
>## ✅ GROUP BY + HAVING 학습
📖 **공식 문서 참고**
### 14.19.3 MySQL Handling of GROUP BY
- 🔗 [MySQL 공식 문서 - GROUP BY](https://dev.mysql.com/doc/refman/8.0/en/group-by-handling.html) ([`ONLY_FULL_GROUP_BY`](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_only_full_group_by))

SQL에서 GROUP BY를 쓸 때는 집계 함수(Aggregate function)을 쓰지 않은 **비집계 컬럼(non-aggregated column)**은 반드시 GROUP BY절에 포함되어야 하지만, 기능적 종속(Functional Dependency)을 만족하는 경우, GROUP BY에 명시되지 않은 비집계 컬럼도 SELECT절에 포함하는 것을 허용한다.

#### 기능적 종속 (Functional Dependency)이란?
```sql
students (student_id, student_name, department_id, department_name)
```
위와 같은 테이블에서 `department_id`가 유일하다면, `department_name`은 `department_id`에 기능적으로 종속된다. 즉, `department_id`가 같으면 `department_name`도 항상 같다.

>#### ONLY_FULL_GROUP_BY
#### ONLY_FULL_GROUP_BY 모드가 활성화된 경우
`SQL ONLY_FULL_GROUP_BY 모드`가 활성화된 경우에도 GROUP BY 절에 이름이 지정되지 않은 비집계 컬럼을 SELECT 절에 허용한다. 단, 비집계 컬럼이 단일 값으로 제한되어야 한다.

SELECT 목록에 두 개 이상의 비집계 컬럼이 있다면, 모든 열은 WHERE 절에서 단일 값으로 제한되어야 하며, 이러한 모든 제한 조건은 여기에 표시된 대로 논리적 AND로 결합되어야 한다. 


```sql
SELECT name, address, MAX(age) FROM t GROUP BY name;
-- GROUP BY 절에 address가 없기 때문에 문제 발생.
-- 하지만 name이 t의 Primary Key인 경우, 서버에서 스스로 기능적 종속성을 인식하여 문제가 발생하지 않기도 한다!
```
```sql
-- address를 GROUP BY에 추가하여 문제 해결
SELECT name, address, MAX(age) FROM t GROUP BY name, address;
```

비집계 컬럼에 `ANY_VALUE()`를 사용하여도 문제를 해결할 수 있다.
```sql
SELECT name, ANY_VALUE(address), MAX(age) FROM t GROUP BY name;
```
ONLY_FULL_GROUP_BY 모드를 비활성화하는 경우도 있다. 
```sql
SET SESSION sql_mode = (SELECT REPLACE(@@sql_mode, 'ONLY_FULL_GROUP_BY', ''));
-- 세션 수준에서 임시 비활성화

SELECT name, address, MAX(age)
FROM t
GROUP BY name;
```

#### ONLY_FULL_GROUP_BY 모드가 비활성화된 경우
컬럼이 GROUP BY 컬럼에 기능적으로 종속되지 않더라도 select list, HAVING 조건 또는 ORDER BY 목록이 집계되지 않은 열을 참조할 수 있도록 허용한다.<br>
-> GROUP BY에 없는 컬럼을 SELECT에 쓰면, 그 컬럼의 값 중 하나가 그룹 안에서 무작위로 선택될 수 있다. ORDER BY는 결과 전체의 정렬 순서를 정할 뿐, 그룹 안에서 "어떤 값이 선택될지"를 결정하지 않는다.

#### MYSQL에서는 `HAVING 절`에 별칭(alias) 사용 가능
```sql
SELECT name, COUNT(name) AS c FROM orders
  GROUP BY name
  HAVING c = 1;
```
GROUP BY에서도 별칭을 사용할 수 있다.
```sql
SELECT id, FLOOR(value/100) AS val
  FROM tbl_name
  GROUP BY id, val;
```

⚠️표현식끼리의 기능적 종속 관계는 MySQL이 인식하지 못한다.
```sql
SELECT id, FLOOR(value/100), id + FLOOR(value/100)
FROM tbl
GROUP BY id, FLOOR(value/100);
-- id + FLOOR(value/100)이 GROUP BY에 없어서 오류 발생
```
-> 서브쿼리 사용하여 문제 해결
```sql
SELECT id, F, id + F
FROM (
  SELECT id, FLOOR(value/100) AS F
  FROM tbl
  GROUP BY id, FLOOR(value/100)
) AS dt;
```
### 15.2.13 SELECT Statement
- 🔗 [MySQL 공식 문서 - HAVING](https://dev.mysql.com/doc/refman/8.0/en/select.html)

#### WHERE vs HAVING
| 항목             | WHERE 절                                         | HAVING 절                                        |
|------------------|--------------------------------------------------|--------------------------------------------------|
| **적용 시점**     | `GROUP BY` 전에 적용                             | `GROUP BY` 후에 적용                             |
| **대상**         | **행(Row)**에 대한 조건 필터링                   | **그룹(Group)**에 대한 조건 필터링               |
| **집계 함수 사용**| 사용할 수 없음                                   | 사용할 수 있음                                   |
| **주 사용 목적**  | 데이터를 그룹화하기 전에 필터링하고 싶을 때 사용 | 그룹화된 결과에서 조건에 맞는 그룹만 보고 싶을 때 사용 |
| **예시**         | `WHERE age > 20`                                 | `HAVING COUNT(*) > 5`                            |

- HAVING 절은 `무조건` GROUP BY 뒤, ORDER BY 앞에 위치하여야 한다.
- HAVING 절의 이름이 불분명한 경우, 경고가 발생한다.
- HAVING 절에서 사용된 컬럼명이 GROUP BY 절과 SELECT 절의 별칭(alias) 양쪽에서 모두 사용되었다면, MySQL은 GROUP BY에 지정된 컬럼으로 우선하여 인식한다.
```sql
SELECT subject, AVG(score) AS score
FROM students
GROUP BY subject
HAVING score > 80;
```
```
SELECT 절에서 AVG(score)에 **별칭 score**를 지정했습니다.

그런데 HAVING score > 80에서는 이 score가 별칭인지, GROUP BY의 컬럼인지 모호할 수 있습니다.

MySQL은 GROUP BY에 사용된 컬럼명을 우선으로 해석하려고 시도합니다.

그런데 이 경우 GROUP BY subject로 되어 있고, score는 GROUP BY에 포함되지 않았으므로, 결국 HAVING에서는 SELECT 절의 별칭 score (즉 AVG(score))로 해석됩니다.
```
>## 🔎 문제 풀이
### 🔗 [programmers - 언어별 개발자 분류하기](https://school.programmers.co.kr/learn/courses/30/lessons/276036) 
```sql
WITH SKILLS AS (
    SELECT
        D.ID,
        D.EMAIL,
        S.NAME,
        S.CATEGORY
    FROM DEVELOPERS D
    JOIN SKILLCODES S
      ON (D.SKILL_CODE & S.CODE) = S.CODE
      -- 비트연산을 통해 보유 스킬 매핑
),
GRADE AS (
    SELECT 
        ID,
        EMAIL,
        CASE 
            WHEN SUM(CASE WHEN NAME = 'Python' THEN 1 ELSE 0 END) > 0
            -- 이 개발자가 Python이라는 스킬을 가진 행이 1개 이상 있는가?
              AND SUM(CASE WHEN CATEGORY = 'Front End' THEN 1 ELSE 0 END) > 0 
            -- 이 개발자가 Front End 범주에 해당하는 스킬을 가진 행이 1개 이상 있는가?
              THEN 'A'
            -- 둘 다 TRUE 면 A
            WHEN SUM(CASE WHEN NAME = 'C#' THEN 1 ELSE 0 END) > 0 THEN 'B'
            WHEN SUM(CASE WHEN CATEGORY = 'Front End' THEN 1 ELSE 0 END) > 0 THEN 'C'
            ELSE NULL
        END AS GRADE
    FROM SKILLS
    GROUP BY ID, EMAIL
)
SELECT GRADE, ID, EMAIL
FROM GRADE
WHERE GRADE IS NOT NULL
ORDER BY GRADE ASC, ID ASC;

-- WHEN S.CATEGORY = 'Front End' AND S.NAME = 'Python' THEN 'A' 조건이 참이 되려면 S.CATEGORY는 'Front End'이고, S.NAME은 'Python'이어야 한다.
```
![image](2025_Advanced/image/2nd_week/3.png)