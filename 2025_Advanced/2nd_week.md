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

- 🔗 [MySQL 공식 문서 - GROUP BY](https://dev.mysql.com/doc/refman/8.0/en/group-by-handling.html) ([`ONLY_FULL_GROUP_BY`](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_only_full_group_by))

- 🔗 [MySQL 공식 문서 - HAVING](https://dev.mysql.com/doc/refman/8.0/en/select.html)

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