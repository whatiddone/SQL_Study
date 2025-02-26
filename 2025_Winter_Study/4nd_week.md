# 8주차
## 1. PIVOT과 UNPIVOT
### 1) pivot과 unpivot에 관해 자유로운 리소스를 이용해 학습하고 요약해주세요.
- PIVOT와 UNPIVOT는 SQL에서 테이블의 행과 열을 변환하는 데 사용되는 연산자입니다.

#### PIVOT
- 행을 열로 바꾼다. 
  - 지정된 칼럼의 각 생 속성값들이 새로운 컬럼의 헤더가 되고, 이에 맞게 전체 속성값들이 재배치된다. 
```sql
SELECT * 
FROM (
    SELECT E.JOB, D.DNAME
    FROM EMP E
    JOIN DEPT D ON E.DEPTNO = D.DEPTNO
) AS SourceTable
PIVOT (
    COUNT(JOB) 
    FOR DNAME IN ([ACCOUNTING], [RESEARCH], [SALES])
) AS PivotTable;

                              
# DNAME에 대해서 COUNT(*)연산을 수행하는 것을 알 수 있는데, PIVOT 내부적으로 보면 첫 번째 컬럼에 대해서 GROUP BY 연산이 수행됨을 포함하고 있다. 따라서 첫 번째 컬럼이 직업(JOB)으로 GROUP BY가 되고, 다시 부서명(DNAME)에 대해서 각 값('ACCOUNTING', 'RESEARCH', 'SALES')별로 COUNT(*)를 수행하게 된다.)
```

#### UNPIVOT
- 열을 행으로 바꾼다.
  - 컬럼 헤더들이 한 컬럼의 각 행 속성값이 되고, 이에 맞게 전체 속성값들이 재배치된다.
```sql
SELECT 계절, 연도, 기온
FROM (SELECT * FROM 평균기온)
UNPIVOT (기온 FOR 연도 IN (Y2018 AS '2018년',
                          Y2019 AS '2019년',
                          Y2020 AS '2020년',
                          Y2021 AS '2021년',
                          Y2022 AS '2022년');
```

### 2) 다음 문제를 풀어주세요
https://www.hackerrank.com/challenges/occupations/problem
```
Pivot the Occupation column in OCCUPATIONS so that each Name is sorted alphabetically and displayed underneath its corresponding Occupation. The output should consist of four columns (Doctor, Professor, Singer, and Actor) in that specific order, with their respective names listed alphabetically under each column.

Note: Print NULL when there are no more names corresponding to an occupation.
```
```sql
SET @D=0, @P=0, @S=0, @A=0;

SELECT MIN(Doctor) AS Doctor, 
       MIN(Professor) AS Professor, 
       MIN(Singer) AS Singer, 
       MIN(Actor) AS Actor
FROM (
    SELECT 
        CASE WHEN Occupation = 'Doctor' THEN Name END AS Doctor,
        CASE WHEN Occupation = 'Professor' THEN Name END AS Professor,
        CASE WHEN Occupation = 'Singer' THEN Name END AS Singer,
        CASE WHEN Occupation = 'Actor' THEN Name END AS Actor,
        CASE 
            WHEN Occupation = 'Doctor' THEN (@D := @D + 1)
            WHEN Occupation = 'Professor' THEN (@P := @P + 1)
            WHEN Occupation = 'Singer' THEN (@S := @S + 1)
            WHEN Occupation = 'Actor' THEN (@A := @A + 1)
        END AS RowNumber
    FROM Occupations 
    ORDER BY Name
) AS sub 
GROUP BY RowNumber;

```

## SQL 쿼리 성능 최적화를 위한 튜닝 팁 6가지
- 잘 튜닝된 쿼리는 같은 작업을 더 빠르고 안정적으로 처리할 수 있게 한다.

### 0. 인덱싱
- 데이터베이스에서 원하는 정보를 찾기 위한 '지름길'

### 1. 좌변을 연산하지 않을 것
```sql
SELECT * 
FROM sales 
WHERE YEAR(date) = 2021;
```
- 데이터 원본을 변형하여, 내가 찾고자 하는 범위와 비교하는 연산은 데이터베이스가 인덱스를 제대로 활용할 수 없게 만든다.

- 위와 같은 코드는 한 줄 한 줄 일일이 YEAR(date) 연산을 수행하고, 그 결과가 2021인지 확인하는 방식

```sql
SELECT * 
FROM sales 
WHERE date >= '2021-01-01' AND date <= '2021-12-31';
```
- 원본 데이터를 직접 비교하는 조건을 사용하는 것이 인덱스를 최대한 활용하고 쿼리 성능을 높이는 핵심 방법이다.

### 2. OR 대신 UNION을 사용할 것
```sql
SELECT * FROM employees 
WHERE department = 'Marketing' OR department = 'IT';
```
- 인덱스는 단일 값에 대한 빠른 검색을 위해 최적화되어 있는데, OR은 여러 값을 동시에 찾아야 한다. → 인덱스의 장점을 살리지 못한다.

- 위와 같은 코드는  한 번의 스캔으로 모든 조건을 확인하는 방식이다. 
```sql
SELECT * FROM employees WHERE department = 'Marketing'
UNION
SELECT * FROM employees WHERE department = 'IT';
```
- 각 쿼리가 각각 인덱스를 통해 독립적으로, 빠르게 처리된 후, UNION으로 합치는 방식이기 때문에 성능이 높아진다.

### 3. 필요한 Row와 Column만 선택하여 성능 최적화
- 특정 조건을 만족하는 Row만 선택하기
```sql
SELECT name, email
FROM employees
WHERE department = 'Marketing' AND sales >= 100000;
```
- 서브쿼리를 이용하여 필요한 데이터만 추출하기
```sql
SELECT e.name, e.department, e.sales
FROM employees e
JOIN (
  SELECT department, MAX(sales) AS max_sales
  FROM employees
  GROUP BY department
) d ON e.department = d.department AND e.sales = d.max_sales;
```

### 4. 분석 함수를 사용하여 쿼리 성능 높이기
- 분석 함수
  - 각 Row 별로 세부적인 계산을 할 수 있도록 도와준다.
  - ROW_NUMBER(), RANK(), DENSE_RANK(), LEAD(), LAG() 등이 있다.

- 쿼리 효율성을 높이는 분석 함수
  - 전통적인 집계 함수와 달리 사전에 데이터를 그룹화할 필요가 없다.
  - 중간 결과물의 저장과 재처리를 최소화

- 순위 결정 함수로 데이터 순서 매기기
  - `ROW_NUMBER()`: 동일한 값이라도 **순차적으로 고유한 번호를 부여**함.
  - `RANK()`: 동일한 값이면 같은 순위이지만 **다음 순위는 건너뜀**.
  - `DENSE_RANK()`: 동일한 값이면 같은 순위이지만 **순위가 연속됨**.
- 데이터 변화를 추적하는 분석 함수
| 함수  | 설명 | 사용 예시 |
|-------|------|----------|
| `LEAD(column, offset, default) OVER (PARTITION BY col ORDER BY col2)` | 현재 행을 기준으로 '다음' `offset` 번째 행의 값을 반환 | `LEAD(sales, 1, 0) OVER (PARTITION BY region ORDER BY date)` |
| `LAG(column, offset, default) OVER (PARTITION BY col ORDER BY col2)`  | 현재 행을 기준으로 '이전' `offset` 번째 행의 값을 반환 | `LAG(sales, 1, 0) OVER (PARTITION BY region ORDER BY date)` |

- 분석 함수로 데이터 필터링 최적화하기
```sql
# 각 부서별로 급여가 높은 상위 3명의 직원 정보만 추출하기
WITH ranked_employees AS (
  SELECT 
    name, 
    department, 
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
  FROM employees
)
SELECT *
FROM ranked_employees
WHERE rank <= 3;
-- 각 부서 내에서 급여 순위를 매긴 후, 순위가 3 이하인 직원들만 선택하는 방식으로 동작-
```
### 5. 와일드카드(%)는 끝에 작성하는 것이 더 좋다
```sql
SELECT * FROM users WHERE name LIKE '%John';
```
- 와일드카드가 앞에 있으면, 데이터베이스가 'John'으로 끝나는 모든 가능한 문자열 조합을 일일이 검색해야 한다.

- 인덱스가 있어도 제대로 활용할 수 없고 쿼리 속도는 느려지게 된다.

```sql
SELECT * FROM users WHERE name LIKE 'John%';
```
- 데이터베이스가 인덱스를 활용해서 검색 범위를 효과적으로 좁힐 수 있다.

### 6. 계산값을 미리 저장해두었다가, 나중에 조회할 것
- 실시간 계산의 비효율성
```sql
SELECT 
p.product_id,
AVG(od.quantity * od.unit_price) AS avg_order_amount,
SUM(od.quantity * od.unit_price) AS total_sales,
COUNT(DISTINCT o.customer_id) AS num_purchasers,
COUNT(DISTINCT CASE WHEN o.customer_id IN (
    SELECT customer_id 
    FROM orders
    WHERE product_id = p.product_id
    GROUP BY customer_id
    HAVING COUNT(*) > 1
) THEN o.customer_id END) * 1.0 / COUNT(DISTINCT o.customer_id) AS repurchase_rate
FROM 
    products p
    JOIN order_details od ON p.product_id = od.product_id
    JOIN orders o ON od.order_id = o.order_id
GROUP BY 
    p.product_id;
```
  - 실행될 때마다 방대한 양의 주문 및 고객 데이터를 모두 읽어서 복잡한 계산을 수행해야 한다
- 계산값을 저장하고 활용하기
```sql
CREATE TABLE product_stats AS
SELECT
    p.product_id,
    AVG(od.quantity * od.unit_price) AS avg_order_amount,
    SUM(od.quantity * od.unit_price) AS total_sales,
    COUNT(DISTINCT o.customer_id) AS num_purchasers,
    COUNT(DISTINCT CASE WHEN o.customer_id IN (
        SELECT customer_id
        FROM orders
        WHERE product_id = p.product_id
        GROUP BY customer_id
        HAVING COUNT(*) > 1
    ) THEN o.customer_id END) * 1.0 / COUNT(DISTINCT o.customer_id) AS repurchase_rate
FROM
    products p
    JOIN order_details od ON p.product_id = od.product_id
    JOIN orders o ON od.order_id = o.order_id
GROUP BY
    p.product_id;
```
  - product_stats라는 새 테이블을 만들고, 앞서 본 복잡한 계산을 수행

- 주기적인 계산 결과 업데이트
```sql
UPDATE product_stats ps
SET
    avg_order_amount = (
        SELECT AVG(od.quantity * od.unit_price)
        FROM order_details od
        WHERE od.product_id = ps.product_id
    ),
    total_sales = (
        SELECT SUM(od.quantity * od.unit_price)
        FROM order_details od
        WHERE od.product_id = ps.product_id  
    ),
    num_purchasers = (
        SELECT COUNT(DISTINCT o.customer_id)
        FROM order_details od
        JOIN orders o ON od.order_id = o.order_id
        WHERE od.product_id = ps.product_id
    ),
    repurchase_rate = (
        SELECT 
            COUNT(DISTINCT CASE WHEN o.customer_id IN (
                SELECT customer_id
                FROM orders
                WHERE product_id = ps.product_id
                GROUP BY customer_id
                HAVING COUNT(*) > 1
            ) THEN o.customer_id END) * 1.0 / COUNT(DISTINCT o.customer_id)
        FROM order_details od 
        JOIN orders o ON od.order_id = o.order_id
        WHERE od.product_id = ps.product_id
    );
```
  - product_stats 테이블의 통계치들을 최신 주문 내역을 바탕으로 업데이트하는 쿼리

- 이런 식으로 계산 결과를 저장하고 주기적으로 업데이트하면, 복잡한 실시간 쿼리의 부담을 크게 줄일 수 있음

## 문제 풀이
### 문제
```
여러분은 customer_orders라는 테이블을 관리하는 데이터베이스 관리자로 일하고 있습니다. 이 테이블에는 고객의 주문 정보가 저장되어 있으며, 각 고객이 주문한 제품과 수량, 가격 정보가 포함되어 있습니다. 또한, 고객들이 특정 제품을 재구매한 비율을 계산하려고 합니다.
```

### 테이블 구조:

1. **customers** 테이블
    - `customer_id` (고객 ID, PRIMARY KEY)
    - `name` (고객 이름)
2. **orders** 테이블
    - `order_id` (주문 ID, PRIMARY KEY)
    - `customer_id` (고객 ID, FOREIGN KEY)
    - `order_date` (주문 날짜)
3. **order_details** 테이블
    - `order_id` (주문 ID, FOREIGN KEY)
    - `product_id` (제품 ID)
    - `quantity` (수량)
    - `unit_price` (단가)

### 요구 사항:

1. **`avg_order_value`**: 고객별 평균 주문 금액을 계산하여 `customers` 테이블에 업데이트하세요.
    - `avg_order_value`는 각 고객이 한 번의 주문에서 지출한 평균 금액입니다.
2. **`total_spent`**: 고객별 총 지출 금액을 계산하여 `customers` 테이블에 업데이트하세요.
    - `total_spent`는 고객이 지금까지 지출한 총 금액입니다.
3. **`num_orders`**: 고객이 총 몇 번의 주문을 했는지 계산하여 `customers` 테이블에 업데이트하세요.
    - `num_orders`는 고객이 주문한 총 개수입니다.
4. **`repurchase_rate`**: 고객의 재구매 비율을 계산하여 `customers` 테이블에 업데이트하세요.
    - `repurchase_rate`는 각 고객이 2번 이상 주문한 제품 비율을 의미합니다. (즉, 재구매한 제품이 전체 구매 제품 중 몇 퍼센트를 차지하는지)

### 예시:

- 고객 A는 3번 주문을 했고, 그 중 2개의 제품을 재구매했습니다.
    - 평균 주문 금액: 100,000원
    - 총 지출 금액: 300,000원
    - 주문 횟수: 3번
    - 재구매 비율: 66.67%

### 정답
```sql
UPDATE customers as c
SET
    avg_order_value = (
        SELECT AVG(od.quantity * od.unit_price)
        FROM order_details as od
        JOIN orders o ON od.order_id = o.order_id
        WHERE o.customer_id = c.customer_id
    ), --avg_order_value 계산
    total_spent = (
        SELECT SUM(od.quantity * od.unit_price)
        FROM order_details od
        JOIN orders o ON od.order_id = o.order_id
        WHERE o.customer_id = c.customer_id
    ), --total_spent 계산
    num_orders = (
        SELECT COUNT(DISTINCT o.order_id)
        FROM orders o
        WHERE o.customer_id = c.customer_id
    ), --num_orders 계산--
    repurchase_rate = (
        SELECT
            COUNT(DISTINCT CASE WHEN od.product_id IN (
                SELECT product_id
                FROM order_details
                JOIN orders o ON order_details.order_id = o.order_id
                WHERE o.customer_id = c.customer_id
                GROUP BY order_details.product_id
                HAVING COUNT(order_details.product_id) > 1
            ) THEN od.product_id END) * 1.0 / COUNT(DISTINCT od.product_id)
        FROM order_details od
        JOIN orders o ON od.order_id = o.order_id
        WHERE o.customer_id = c.customer_id
    );
```
- 집계 함수를 쓴 뒤에 GROUP BY를 쓰지 않는 이유
  - WHERE 절을 사용하여 각 고객별로 하나의 값을 반환하기 때문
  - 만약 전체 고객을 한 번에 조회하려고 하면, GROUP BY가 필요
# 정연 문제
## 문제 1
### [SELECT] 강원도에 위치한 생산공장 목록 출력하기 (https://school.programmers.co.kr/learn/courses/30/lessons/131112)
```sql
SELECT
     FACTORY_ID,
     FACTORY_NAME,
     ADDRESS
FROM FOOD_FACTORY
WHERE ADDRESS like '강원도%'
```
- %는 LIKE 연산자에서만 사용 가능
## 문제 2
### [GROUP BY] 특정 조건을 만족하는 물고기별 수와 최대 길이 구하기 (https://school.programmers.co.kr/learn/courses/30/lessons/298519)
```sql
SELECT
    COUNT(DISTINCT ID) AS FISH_COUNT,
    MAX(LENGTH) AS MAX_LENGTH,
    FISH_TYPE        
FROM (
    SELECT *
    FROM FISH_INFO
    IF LENGTH IS NULL then 10)     
GROUP BY FISH_TYPE
HAVING AVG(LENGTH) >= 33
ORDER BY FISH_TYPE;
```
- 오류
    -  SQL에서 IF ... THEN 구문은 SELECT 문과 서브쿼리 안에서 사용할 수 없다. → COALESCE(LENGTH, 10) 또는 CASE WHEN을 사용
    - NULL 값이 있는 경우 AVG()가 올바르게 계산되지 않을 수도 있다.→ COALESCE()로 NULL을 방지

#### 🎯 NULLIF vs COALESCE vs IFNULL 차이점 정리

| 연산자 | 기능 | 입력 개수 | 반환 값 | 사용 예시 | 지원 DB |
|--------|------|----------|---------|----------|---------|
| **NULLIF(value1, value2)** | 두 값이 같으면 `NULL` 반환, 다르면 `value1` 반환 | 2개 | `NULL` 또는 `value1` | `NULLIF(10, 10) → NULL`<br>`NULLIF(10, 20) → 10` | MySQL, PostgreSQL, SQL Server, Oracle |
| **COALESCE(value1, value2, ...)** | `NULL`이 아닌 첫 번째 값을 반환 | 2개 이상 | 첫 번째 `NULL`이 아닌 값 | `COALESCE(NULL, 20, 30) → 20` | MySQL, PostgreSQL, SQL Server, Oracle |
| **IFNULL(value1, value2)** | `value1`이 `NULL`이면 `value2` 반환 | 2개 | `value1`이 `NULL`이면 `value2`, 아니면 `value1` | `IFNULL(NULL, 10) → 10`<br>`IFNULL(20, 10) → 20` | MySQL, MariaDB |

#### 🔥 **각 함수의 동작 방식 예제**
```sql
SELECT NULLIF(10, 10);       -- 결과: NULL
SELECT NULLIF(10, 20);       -- 결과: 10

SELECT COALESCE(NULL, 20, 30);  -- 결과: 20
SELECT COALESCE(NULL, NULL, 30); -- 결과: 30

SELECT IFNULL(NULL, 10);      -- 결과: 10
SELECT IFNULL(20, 10);        -- 결과: 20
```

#### 💡 결론:

- NULLIF → 두 값이 같으면 NULL 반환
- COALESCE → NULL이 아닌 첫 번째 값 반환 (여러 개의 값 비교 가능)
- IFNULL → NULL이면 두 번째 값 반환 (MySQL 전용)

```sql
SELECT 
    COUNT(*) AS FISH_COUNT
    , MAX(LENGTH) AS MAX_LENGTH
    , FISH_TYPE
FROM FISH_INFO
GROUP BY FISH_TYPE
HAVING AVG(IFNULL(LENGTH, 10)) >= 33
ORDER BY FISH_TYPE;
```

## 문제 3
### 제목이 모음으로 끝나지 않는 영화 (https://solvesql.com/problems/film-ending-with-consonant/)
```sql
SELECT title
FROM 
  (SELECT title, rating
  FROM film
  WHERE rating IN ('R', 'NC-17')
  ) as sq
WHERE title NOT LIKE '%A'
AND title NOT LIKE '%E'
AND title NOT LIKE '%I'
AND title NOT LIKE '%O'
AND title NOT LIKE '%U'
```
## 문제 4
### 버뮤다 삼각지대에 들어가버린 택배 (https://solvesql.com/problems/shipment-in-bermuda/)
```sql
WITH orders_201701 AS (
    SELECT * 
    FROM olist_orders_dataset
    WHERE order_delivered_carrier_date BETWEEN '2017-01-01' AND '2017-02-01'
    AND order_delivered_customer_date IS NULL 
)
SELECT 
  date(order_delivered_carrier_date) AS delivered_carrier_date,
  count(*) as orders 
FROM orders_201701
GROUP BY delivered_carrier_date 
ORDER BY delivered_carrier_date ASC;
```
- 다른 풀이
```sql
SELECT 
    DATE(order_delivered_carrier_date) AS delivered_carrier_date,
    COUNT(*) AS orders
FROM olist_orders_dataset
WHERE order_delivered_carrier_date >= '2017-01-01'
  AND order_delivered_carrier_date < '2017-02-01'
  AND order_delivered_customer_date IS NULL
GROUP BY DATE(order_delivered_carrier_date)
ORDER BY delivered_carrier_date;
```