# 0주차

>## 📖 서브쿼리 학습 및 문제 풀이

📖 **공식 문서 참고**: 🔗 [MySQL 공식 문서 - 서브쿼리](https://dev.mysql.com/doc/refman/8.0/en/subqueries.html)

### 15.2.15. Subqueries
서브쿼리의 주요 장점

- 쿼리를 쉽게 구조화한다.

- JOIN과 UNION을 쉽게 대체할 수 있으며 구조적인 쿼리문을 만들 수 있다.

서브쿼리의 주요 형태

Example
```sql
SELECT * FROM t1 WHERE column1 
= (SELECT column1 FROM t2) # subquery;
```
### 15.2.15.2. Comparisons Using Subqueries
```
non_subquery_operand comparison_operator (subquery)
```

- 단일 비교 연산자
```sql
=  >  <  >=  <=  <>  !=  <=>
```
SQL에서 **단일 비교 연산자(Single Comparison Operators)**는 두 개의 값을 비교해 **참(True) 또는 거짓(False)**을 반환하는 연산자입니다. 주로 WHERE 절에서 조건을 지정할 때 사용됩니다.

### 15.2.15.3. Subqueries with ANY, IN or SOME
```sql
operand comparison_operator ANY (subquery)
operand IN (subquery)
operand comparison_operator SOME (subquery)
```

1️⃣ IN
의미: 서브쿼리 결과 중 하나라도 일치하면 TRUE
``` sql
SELECT name
FROM Employees
WHERE department_id IN (SELECT id FROM Departments WHERE location = 'Seoul');
```
✔ Seoul에 있는 부서의 직원만 조회

2️⃣ ANY/SOME
의미: 서브쿼리 결과 값 중 하나라도 조건을 만족하면 TRUE   
- ANY와 SOME은 완전히 동일하게 작동
- *= ANY (서브쿼리)* ➔ 사실상 IN (서브쿼리)와 동일하게 작동
```sql
SELECT name
FROM Employees
WHERE salary > ANY (SELECT salary FROM Employees WHERE department_id = 10);
```
✔ 10번 부서 사람들보다 급여가 높은 직원 조회

✅ 포인트 정리
| 구문              | 의미                                                                 | 비고                                 |
|-------------------|----------------------------------------------------------------------|--------------------------------------|
| `IN (서브쿼리)`    | 서브쿼리 결과 중 **하나와 일치**하면 참                               | `= ANY (서브쿼리)`와 동일            |
| `= ANY (서브쿼리)` | 서브쿼리 결과 중 **하나와 같으면** 참                                | `IN`과 동일                          |
| `<> ANY (서브쿼리)`| 서브쿼리 결과 중 **하나와 다르면** 참                                | 모두 달라야 참이 아님 → 하나만 달라도 참 |
| `> ANY (서브쿼리)` | 서브쿼리 결과 중 **최소 하나보다 크면** 참                           | "한 명이라도 나보다 급여 낮으면 참" |
| `< ANY (서브쿼리)` | 서브쿼리 결과 중 **최소 하나보다 작으면** 참                         | "한 명이라도 나보다 급여 높으면 참" |
| `>= ANY (서브쿼리)`| 서브쿼리 결과 중 **최소 하나보다 크거나 같으면** 참                   |                                      |
| `<= ANY (서브쿼리)`| 서브쿼리 결과 중 **최소 하나보다 작거나 같으면** 참                   |                                      |
| `SOME (서브쿼리)`  | `ANY`와 완전히 동일 (ANSI SQL 표준)                                  | `ANY`와 호환                         |


### 15.2.15.4. Subqueries with ALL
```sql
operand comparison_operator ALL (subquery)
```
ALL은 서브쿼리에서 반환된 모든 값에 대해 비교한다.

| 구문               | 의미                                                               | 비고                                         |
|--------------------|--------------------------------------------------------------------|----------------------------------------------|
| `= ALL (서브쿼리)`  | 서브쿼리 결과 **전부와 같아야** 참                                | 서브쿼리 결과가 모두 같은 값일 때만 참      |
| `<> ALL (서브쿼리)` | 서브쿼리 결과 **전부와 달라야** 참                                | 전부 다르지 않으면 거짓                     |
| `NOT IN (서브쿼리)` | 서브쿼리 결과 **전부와 달라야** 참                                | 전부 다르지 않으면 거짓                     |
| `> ALL (서브쿼리)`  | 서브쿼리 결과 **전부보다 커야** 참                                | "모든 값보다 커야 참"                       |
| `< ALL (서브쿼리)`  | 서브쿼리 결과 **전부보다 작아야** 참                              | "모든 값보다 작아야 참"                     |
| `>= ALL (서브쿼리)` | 서브쿼리 결과 **전부보다 크거나 같아야** 참                       |                                              |
| `<= ALL (서브쿼리)` | 서브쿼리 결과 **전부보다 작거나 같아야** 참                       |                                              |

📌 **ALL 비교구문을 사용할 때 서브쿼리 테이블이 비어있거나, NULL 값이 있다면?**
>1. 서브쿼리 결과가 비어있을 때 (Empty Set)
- 결과: 항상 TRUE

>2. 서브쿼리 결과에 NULL 값이 있을 때
- 결과: NULL이 섞여 있으면 FALSE가 아니라 결과가 UNKNOWN (NULL) 이 돼버림 → WHERE 절에서 무시

이유: NULL과의 비교는 무조건 UNKNOWN   
ALL은 서브쿼리 결과 중 비교가 FALSE인 게 없고, 나머지가 전부 TRUE여야 TRUE  
근데 NULL이 끼면 → TRUE도 아니고 FALSE도 아니라 → 전체 결과가 UNKNOWN

>2-1. 해결 방법

| 상황           | 해결방법                           | 
|--------------------|----------------------------------------------|
| NULL 회피	  | 서브쿼리에서 `NULL 제거` → WHERE bonus IS NOT NULL    | 
| 비어있는 경우 예외 처리	 | `EXISTS`, `COALESCE`, `CASE WHEN` 사용   | 

### 15.2.15.6. Subqueries with EXISTS or NOT EXISTS
```sql
SELECT column1 FROM t1 WHERE EXISTS (subquery);
```

| 구문 | 의미                        | 
|--------------------|----------------------------------------------|
| `EXIST`	  | 서브쿼리 결과가 **하나라도 존재하면** 참    | 
| `NOT EXIST` | 서브쿼리 결과가 **없어야** 참   |
| `NULL 영향` | NULL 여부 상관없이 **존재하면** 참   |

- 예시
  ```sql
  # 보너스를 받은 직원만 조회
  SELECT name
  FROM Employees e
  WHERE EXISTS (
    SELECT 1
    FROM BonusTable b
    WHERE b.emp_id = e.emp_id);
  ```
  ```sql
  # 보너스를 한 번도 못 받은 직원만 조회
  SELECT name
  FROM Employees e
  WHERE NOT EXISTS (
    SELECT 1
    FROM BonusTable b
    WHERE b.emp_id = e.emp_id);
  ```

### 15.2.15.10. Subquery Errors
>1. Unsupported subquery syntax
```sql
SELECT * FROM t1 WHERE s1 IN (SELECT s2 FROM t2 ORDER BY s1 LIMIT 1)
```
→ IN 서브쿼리 안에서 ORDER BY를 사용했기 때문에 오류 발생

- 해결 방법   
  - IN 대신 = 를 사용하여 단일 비교(스칼라 서브쿼리)로 변환
  - ORDER BY 제거 
  - CTE 사용

>2. Incorrect number of columns from subquery
```sql
SELECT (SELECT column1, column2 FROM t2) FROM t1;
```
- 해결 방법
  - 서브쿼리에서 하나의 컬럼만 반환하게 수정
  - 여러 컬럼(튜플)을 하나의 값처럼 묶어주는 함수인 ROW() 사용

>3. Incorrect number of rows from subquery
```sql
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```
- 해결 방법
  - 서브쿼리에서 하나의 행만 반환하게 수정
  - 여러 값을 비교할 수 있도록 `=` 대신 `IN` 사용

>4. Incorrectly used table in subquery
```sql
UPDATE t1 SET column2 = (SELECT MAX(column1) FROM t1);
```
→ t1을 업데이트하는 동시에 t1을 읽으려고 해서 오류 발생(MySQL은 같은 테이블을 업데이트하면서 동시에 서브쿼리에서 읽는 것을 금지)

- 해결 방법
  - CTE 사용

### 문제 풀이
#### 📝 Solvesql - 많이 주문한 테이블[🔗](https://solvesql.com/problems/find-tables-with-high-bill/)
```sql
SELECT *
FROM tips
WHERE total_bill > (SELECT AVG(total_bill) FROM tips)
```

![image](../2025_Advanced/image/pre_week/1.png)

#### 📝 Solvesql - 레스토랑의 대목[🔗](https://solvesql.com/problems/high-season-of-restaurant/)
```sql
SELECT
  *
FROM tips
WHERE day IN (SELECT day 
              FROM tips 
              GROUP BY day 
              HAVING SUM(total_bill) >= 1500)
```
![image](../2025_Advanced/image/pre_week/2.png)



>## 📖 CTE (WITH) 학습 및 문제 풀이

📖 **공식 문서 참고**: 🔗 [MySQL 공식 문서 - 서브쿼리](https://dev.mysql.com/doc/refman/8.0/en/with.html)

### CTE란? 
```sql
with_clause:
    WITH [RECURSIVE]
        cte_name [(col_name [, col_name] ...)] AS (subquery)
        [, cte_name [(col_name [, col_name] ...)] AS (subquery)] ...
```
- WITH 절을 사용해 임시 결과 집합(temporary result set)을 만들고, 이를 쿼리 내에서 재사용하는 구조
- 가독성과 유지보수성이 좋아지고, 복잡한 쿼리를 단계별로 쪼개서 작성 가능
- 한 쿼리에 여러 CTE를 사용할 수 있으며, 각 CTE는 `콤마(,)`로 연결되어야 한다. 
- CTE는 재사용 가능하며, 스스로 재사용되거나(self-referencing CTE), 다른 CTE에서 재사용될 수 있다.
  - 단, 같은 with 절 내에서 먼저 선언되었거나, 더 상위 레벨 쿼리 블록*에서 정의된 CTE만 참조할 수 있다. *쿼리 블록: 하나의 SELECT ~ FROM ~ WHERE ~ 묶음
  ```sql
  WITH outer_cte AS (SELECT ... ) -- 상위 쿼리 블록
  SELECT *
  FROM ( 
    WITH inner_cte AS (SELECT ... )
    SELECT * FROM inner_cte, outer_cte  -- 하위 쿼리 블록
  ) AS subquery;
  ```

### FROM 절 서브쿼리(파생 테이블)에 비해 CTE의 장점
- 파생 테이블은 쿼리 내에서 단 한 번만 참조할 수 있으나 CTE는 무제한 재사용 가능
- CTE는 자체 참조(재귀적)할 수 있으며, 한 CTE는 다른 CTE를 참조할 수 있다.
- 구조적으로 읽기 쉬움(제일 앞에 위치)
### 문제 풀이
#### 📝 programmers - 식품분류별 가장 비싼 식품의 정보 조회하기[🔗](https://school.programmers.co.kr/learn/courses/30/lessons/131116) 
```sql
# CTE 사용
WITH cte AS(
    SELECT *, 
    MAX(PRICE) AS MAX_PRICE
FROM FOOD_PRODUCT 
GROUP BY CATEGORY)
SELECT CATEGORY,
    MAX_PRICE, 
    PRODUCT_NAME
FROM cte
WHERE CATEGORY IN 
    ('과자', '국', '김치', '식용유')
ORDER BY PRICE DESC;
```
```sql
SELECT CATEGORY, PRICE AS MAX_PRICE, PRODUCT_NAME
FROM FOOD_PRODUCT
WHERE CATEGORY IN ('과자', '국', '김치', '식용유')
  AND PRICE = (
      SELECT MAX(PRICE)
      FROM FOOD_PRODUCT AS subquery
      WHERE subquery.CATEGORY = FOOD_PRODUCT.CATEGORY
  )
ORDER BY PRICE DESC;
```
```sql
# FROM 절 서브쿼리(파생테이블) 사용
SELECT
    CATEGORY,
    PRICE AS MAX_PRICE,
    PRODUCT_NAME
FROM FOOD_PRODUCT
WHERE (CATEGORY, PRICE) IN (
    SELECT
        CATEGORY,
        MAX(PRICE)
    FROM FOOD_PRODUCT
    WHERE CATEGORY IN ('과자', '국', '김치', '식용유')
    GROUP BY CATEGORY
)
ORDER BY PRICE DESC;
```
![image](../2025_Advanced/image/pre_week/3.png)
