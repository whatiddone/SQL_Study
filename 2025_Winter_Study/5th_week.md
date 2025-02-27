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
```