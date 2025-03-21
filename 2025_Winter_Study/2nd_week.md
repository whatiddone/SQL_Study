# 4주차
## 문제 1
### ROOT 아이템 구하기
```sql
SELECT
  I.ITEM_ID, I.ITEM_NAME
FROM ITEM_INFO AS I
LEFT JOIN ITEM_TREE AS T
ON I.ITEM_ID=T.ITEM_ID
WHERE T.PARENT_ITEM_ID IS NULL;
```

## 문제 2
### 노선별 평균 역 사이 거리 조회하기
```sql
SELECT
    ROUTE,
    CONCAT(ROUND(SUM(D_BETWEEN_DIST),2), 'km') AS TOTAL_DISTANCE,
    CONCAT(ROUND(AVG(D_BETWEEN_DIST),3), 'km') AS AVERAGE_DISTANCE
FROM SUBWAY_DISTANCE
GROUP BY ROUTE
ORDER BY TOTAL_DISTANCE DESC;
```
- 답은 똑같이 나오나, SELECT 절에서 CONCAT 함수 때문에 2,3행이 문자열 타입으로 바뀌었다. 문자열의 내림차순은 숫자의 내림차순과 다르기 때문에, 수정이 필요하다.
  - 10, 6 DESC → 10,6
  - '10', '6' DESC → 6, 10 (문자열은 사전순 배열)

#### 해결방법 1
- **숫자값**인 SUM(D_BETWEEN_DIST)로 내림차순 정렬하여 문제 해결
```sql
SELECT
    ROUTE,
    CONCAT(ROUND(SUM(D_BETWEEN_DIST), 1), 'km') AS TOTAL_DISTANCE,
    CONCAT(ROUND(AVG(D_BETWEEN_DIST), 2), 'km') AS AVERAGE_DISTANCE
FROM SUBWAY_DISTANCE
GROUP BY ROUTE
ORDER BY SUM(D_BETWEEN_DIST) DESC;
```
#### 해결방법 2
- 서브쿼리 사용
```sql
SELECT
    ROUTE,
    CONCAT(TOTAL_DISTANCE, 'km') AS TOTAL_DISTANCE,
    CONCAT(AVERAGE_DISTANCE, 'km') AS AVERAGE_DISTANCE
FROM(
  SELECT
    ROUTE,
    ROUND(SUM(D_BETWEEN_DIST),1) AS TOTAL_DISTANCE,
    ROUND(AVG(D_BETWEEN_DIST),2) AS AVERAGE_DISTANCE
  FROM SUBWAY_DISTANCE
  GROUP BY ROUTE
  ORDER BY TOTAL_DISTANCE DESC
) AS subquery;
```

- CONCAT() 함수는 SQL에서 여러 문자열을 하나의 문자열로 결합하는 데 사용

## 문제 3
### 헤비 유저가 소유한 장소
#### IN 연산자 활용
```sql
SELECT 
  ID, 
  NAME, 
  HOST_ID
FROM PLACES
WHERE HOST_ID IN (
    SELECT HOST_ID
    FROM PLACES
    GROUP BY HOST_ID
    HAVING COUNT(*) >= 2
)
ORDER BY ID ASC;
```
- IN 연산자: 지정된 목록(서브쿼리 또는 명시적 값 목록)에 속하는 값인지 확인

#### JOIN 활용
```sql
SELECT
  p.ID, 
  p.NAME, 
  p.HOST_ID
FROM PLACES AS p
JOIN (
  SELECT HOST_ID
  FROM PLACES
  GROUP BY HOST_ID
  HAVING COUNT(*) >= 2
) AS HEAVY_USERS
ON p.HOST_ID = HEAVY_USERS.HOST_ID
ORDER BY p.ID ASC;
```

- IN을 사용한 쿼리는 헤비 유저 리스트를 따로 조회 후 필터링하는 방식.
- JOIN을 사용하면 헤비 유저 정보를 한 번에 합쳐서 조회.

# 5주차
## 문제 1
### 성분으로 구분한 아이스크림 총 주문량
```sql
SELECT 
    I.INGREDIENT_TYPE, 
    SUM(F.TOTAL_ORDER) AS TOTAL_ORDER
FROM FIRST_HALF F
JOIN ICECREAM_INFO I
ON F.FLAVOR = I.FLAVOR
GROUP BY I.INGREDIENT_TYPE
```

## 문제 2
### 즐겨찾기가 가장 많은 식당 정보 출력하기
```sql
SELECT 
    FOOD_TYPE, 
    REST_ID, 
    REST_NAME, 
    FAVORITES
FROM REST_INFO
WHERE FOOD_TYPE = '한식' 
AND FAVORITES = (SELECT MAX(FAVORITES) FROM REST_INFO WHERE FOOD_TYPE = '한식')

UNION

SELECT 
    FOOD_TYPE, 
    REST_ID, 
    REST_NAME, 
    FAVORITES
FROM REST_INFO
WHERE FOOD_TYPE = '일식' 
AND FAVORITES = (SELECT MAX(FAVORITES) FROM REST_INFO WHERE FOOD_TYPE = '일식')

UNION

SELECT 
    FOOD_TYPE, 
    REST_ID, 
    REST_NAME, 
    FAVORITES
FROM REST_INFO
WHERE FOOD_TYPE = '양식' 
AND FAVORITES = (SELECT MAX(FAVORITES) FROM REST_INFO WHERE FOOD_TYPE = '양식')

ORDER BY FOOD_TYPE DESC;
```
- 문제에서 원하는 풀이는 아닌 것 같다...

## 문제 3
### 조건에 맞는 사원 정보 조회하기
```sql
SELECT 
    G.SCORE, 
    E.EMP_NO, 
    E.EMP_NAME,
    E.POSITION,
    E.EMAIL
FROM HR_EMPLOYEES AS E
JOIN (
    SELECT EMP_NO, SUM(SCORE) AS SCORE
    FROM HR_GRADE
    GROUP BY EMP_NO
    ORDER BY SCORE DESC
    LIMIT 1
) AS G 
ON E.EMP_NO = G.EMP_NO
ORDER BY G.SCORE DESC;
```

```
🔍 WHERE 절에 들어갈 수 있는 조건의 유형
1️⃣ 단일 값만 허용되는 경우 (=, >, <, >=, <= 등 비교 연산자)
2️⃣ 다중 값이 허용되는 경우 (IN, EXISTS)
```
