# 예연 문제
## 문제 1
### [SELECT] 대장균의 크기에 따라 분류하기 1 (https://school.programmers.co.kr/learn/courses/30/lessons/299307)
```sql
SELECT
 ID, SIZE
FROM (
  SELECT
    *,
    CASE
      WHEN SIZE_OF_COLONY <=50 THEN "LOW"
      WHEN SIZE_OF_COLONY >1000 THEN "HIGH"       
      ELSE "MEDIUM"
      END AS SIZE
  FROM ECOLI_DATA
) as SQ
```

## 문제 2
### [SELECT] 대장균의 크기에 따라 분류하기 2 (https://school.programmers.co.kr/learn/courses/30/lessons/301649)
```sql
WITH Rank AS (
  SELECT
    ID,
    SIZE_OF_COLONY,
    PERCENT_RANK() OVER (ORDER BY SIZE_OF_COLONY DESC) AS rank_pct
  FROM ECOLI_DATA
)
SELECT
  ID,
  CASE
    WHEN rank_pct <= 0.25 THEN 'CRITICAL'
    WHEN rank_pct <= 0.50 THEN 'HIGH'
    WHEN rank_pct <= 0.75 THEN 'MEDIUM'
    ELSE 'LOW'
  END AS SIZE_CATEGORY
FROM Ranked
ORDER BY ID ASC;
```
- 📌 PERCENT_RANK() 함수란?
  - PERCENT_RANK()는 백분위 순위를 계산하는 윈도우 함수(Analytic Function)로 특정 값이 전체 데이터에서 상대적으로 어느 위치에 있는지 0~1 사이의 값으로 반환한다. 
```sql
PERCENT_RANK() OVER (ORDER BY 컬럼 [ASC|DESC])
```
#### 📌 **특징**
| 특징 | 설명 |
|------|------|
| **백분위 순위 계산** | 값이 전체 데이터에서 상대적으로 어디에 위치하는지 **0~1 사이 값으로 표현** |
| **내림차순 정렬 가능** | `ORDER BY 컬럼 DESC` 사용 가능 |
| **0.0부터 시작** | 가장 큰 값은 `0.0`, 가장 작은 값은 `1.0` |
| **중복 값이 있어도 연속 증가** | 동일한 값이 있더라도 고르게 분포 |
  - 예) PERCENT_RANK() OVER (ORDER BY SIZE_OF_COLONY DESC)
```
rank_pct <= 0.25 → 상위 25% → 'CRITICAL'
rank_pct <= 0.50 → 상위 26%~50% → 'HIGH'
rank_pct <= 0.75 → 상위 51%~75% → 'MEDIUM'
나머지(76%~100%) → 'LOW'
```
## 문제 3
### 우유와 요거트가 담긴 장바구니 (https://school.programmers.co.kr/learn/courses/30/lessons/62284)
```sql
# Yogurt와 Milk가 있는 행만 남긴 뒤, CART_ID로 묶고, NAME이 2개(Milk, Yogurt) 이상인 행만 남기기

SELECT CART_ID
FROM CART_PRODUCTS
WHERE NAME IN ('Yogurt', 'Milk')
GROUP BY CART_ID
HAVING COUNT(DISTINCT NAME) >= 2
ORDER BY CART_ID ASC;
```
```sql
# INTERSECT(교집합) 이용

SELECT CART_ID
FROM CART_PRODUCTS
WHERE NAME = "Milk"
INTERSECT
SELECT CART_ID
FROM CART_PRODUCTS
WHERE NAME = "Yogurt"
ORDER BY CART_ID;
```
```sql
# JOIN 활용
SELECT A.CART_ID
FROM CART_PRODUCTS AS A
LEFT JOIN CART_PRODUCTS AS B
ON A.CART_ID = B.CART_ID
WHERE (A.NAME LIKE 'Milk' AND B.NAME LIKE 'Yogurt') 
        OR (A.NAME LIKE 'Yogurt' AND B.NAME LIKE 'Milk')
GROUP BY A.CART_ID
ORDER BY A.CART_ID;
```

## 문제 4
### [JOIN] 없어진 기록 찾기 (https://school.programmers.co.kr/learn/courses/30/lessons/59042)
```sql
SELECT
    A.ANIMAL_ID, A.NAME
FROM ANIMAL_OUTS AS A
LEFT JOIN ANIMAL_INS AS B
ON A.ANIMAL_ID = B.ANIMAL_ID
WHERE B.ANIMAL_ID IS NULL
ORDER BY A.ANIMAL_ID;
```

## 문제 5
### [JOIN] 상품을 구매한 회원 비율 구하기 (https://school.programmers.co.kr/learn/courses/30/lessons/131534)
```sql
WITH USER_CTE AS (
    SELECT
        USER_ID,
        YEAR(JOINED) AS YEAR
    FROM USER_INFO 
), 
SALE_CTE AS (
    SELECT
        ONLINE_SALE_ID,
        USER_ID,
        SALES_AMOUNT,
        YEAR(SALES_DATE) AS YEAR,
        MONTH(SALES_DATE) AS MONTH
    FROM ONLINE_SALE 
)
SELECT 
    B.YEAR,
    B.MONTH,
    COUNT(DISTINCT CASE WHEN B.ONLINE_SALE_ID IS NOT NULL THEN A.USER_ID END) AS PURCHASED_USERS,
    ROUND(
        COUNT(DISTINCT CASE WHEN B.ONLINE_SALE_ID IS NOT NULL THEN A.USER_ID END) 
        / (SELECT COUNT(DISTINCT A.USER_ID) FROM USER_CTE WHERE YEAR(JOINED) = 2021), 1) AS PURCHASE_RATIO
FROM USER_CTE AS A
LEFT JOIN SALE_CTE AS B
ON A.USER_ID = B.USER_ID
WHERE A.YEAR = 2021
GROUP BY B.YEAR, B.MONTH
ORDER BY B.YEAR ASC, B.MONTH ASC;
```
→ YEAR과 MONTH는 NULL이지만 PURCHASED_USERS랑 PURCHASE_RATIO는 0으로 나오는 행 존재
- 문제 분석
1. LEFT JOIN으로 인해 구매 이력이 전혀 없는 회원(A.USER_ID만 존재하는 회원) 도 결과에 포함됨.
2. GROUP BY B.YEAR, B.MONTH에서 B.YEAR과 B.MONTH가 NULL인 데이터가 하나의 그룹으로 처리됨.
3. NULL 값이 GROUP BY에서 하나의 그룹으로 인식되어 COUNT(DISTINCT CASE ...)이 0으로 계산됨.

- ✅ 해결 방법
1. WHERE 절에 B.YEAR IS NOT NULL 조건 추가
2. HAVING 절을 활용하여 PURCHASED_USERS > 0인 행만 출력
```sql
WITH USER_CTE AS (
    SELECT
        USER_ID,
        YEAR(JOINED) AS YEAR
    FROM USER_INFO 
), 
SALE_CTE AS (
    SELECT
        ONLINE_SALE_ID,
        USER_ID,
        SALES_AMOUNT,
        YEAR(SALES_DATE) AS YEAR,
        MONTH(SALES_DATE) AS MONTH
    FROM ONLINE_SALE 
),
TOTAL_USERS AS (
    SELECT COUNT(DISTINCT USER_ID) AS TOTAL_USER_COUNT 
    FROM USER_CTE 
    WHERE YEAR = 2021
)
SELECT 
    B.YEAR,
    B.MONTH,
    COUNT(DISTINCT CASE WHEN B.ONLINE_SALE_ID IS NOT NULL THEN A.USER_ID END) AS PURCHASED_USERS,
    ROUND(
        COUNT(DISTINCT CASE WHEN B.ONLINE_SALE_ID IS NOT NULL THEN A.USER_ID END) 
        / (SELECT TOTAL_USER_COUNT FROM TOTAL_USERS), 1
    ) AS PURCHASE_RATIO
FROM USER_CTE AS A
LEFT JOIN SALE_CTE AS B
ON A.USER_ID = B.USER_ID
WHERE A.YEAR = 2021  -- 2021년에 가입한 회원만 필터링
AND B.YEAR IS NOT NULL  -- NULL YEAR 제거
GROUP BY B.YEAR, B.MONTH
HAVING PURCHASED_USERS > 0  -- 구매한 회원이 0명인 행 제거
ORDER BY B.YEAR ASC, B.MONTH ASC;
```


* ALTER을 사용해서 테이블을 업데이트 하는 법
```sql
ALTER TABLE your_table ADD COLUMN year_column INT;

UPDATE your_table
SET year_column = YEAR(your_date_column);
```