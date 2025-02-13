# 6주차
## 문제 1
### [MAX] 즐겨찾기가 가장 많은 식당 정보 출력하기
```
다음은 식당의 정보를 담은 REST_INFO 테이블입니다. REST_INFO 테이블은 다음과 같으며 REST_ID, REST_NAME, FOOD_TYPE, VIEWS, FAVORITES, PARKING_LOT, ADDRESS, TEL은 식당 ID, 식당 이름, 음식 종류, 조회수, 즐겨찾기수, 주차장 유무, 주소, 전화번호를 의미합니다.
REST_INFO 테이블에서 음식종류별로 즐겨찾기수가 가장 많은 식당의 음식 종류, ID, 식당 이름, 즐겨찾기수를 조회하는 SQL문을 작성해주세요. 이때 결과는 음식 종류를 기준으로 내림차순 정렬해주세요.
```
1. 틀린 코드 이유 분석(정답 코드 참고)

**틀린 코드**
```sql
SELECT *
FROM (SELECT FOOD_TYPE, REST_ID, REST_NAME, MAX(FAVORITES) AS FAVORITES
FROM REST_INFO
GROUP BY FOOD_TYPE)
ORDER BY FOOD_TYPE DESC
```

**틀린 이유**
```
FROM 절에 있는 서브쿼리는 반드시 별칭(alias)을 지정해야 한다. 서브쿼리에 별칭이 없으므로 Every derived table must have its own alias 오류가 발생한다.
REST_ID와 REST_NAME은 GROUP BY에 포함되지 않아, 해당 그룹에서 임의의 행(최상단 행)에서 가져오게 되어 잘못된 내용을 불러올 수 있다.
```

**정답 코드**
```sql
SELECT FOOD_TYPE, REST_ID, REST_NAME, FAVORITES
FROM REST_INFO
WHERE (FOOD_TYPE, FAVORITES) IN (
    SELECT FOOD_TYPE, MAX(FAVORITES)    
    FROM REST_INFO
    GROUP BY FOOD_TYPE
) 
ORDER BY FOOD_TYPE DESC;
```

## 문제 2
### 개선된 쿼리 학습
**ROW NUMBER 사용한 쿼리**
```sql
WITH RankedRest AS (
    SELECT 
        FOOD_TYPE, REST_ID, REST_NAME, FAVORITES,
        ROW_NUMBER() OVER (PARTITION BY FOOD_TYPE ORDER BY FAVORITES DESC, REST_ID) AS rnk
    FROM REST_INFO
)
SELECT 
    FOOD_TYPE, REST_ID, REST_NAME, FAVORITES
FROM RankedRest
WHERE rnk = 1
ORDER BY FOOD_TYPE DESC;
```
```
🔍 쿼리 설명
가상 쿼리(윈도우)를 생성한 뒤,
ROW_NUMBER()를 사용하여 각 FOOD_TYPE 그룹 내에서 FAVORITES가 높은 순서대로 rn 값을 부여한다.

PARTITION BY FOOD_TYPE → 음식 종류별로 데이터를 나눠서 순위를 매기기기
ORDER BY FAVORITES DESC → FAVORITES 값이 가장 높은 레스토랑이 rn = 1

메인 쿼리에서 WHERE rn = 1만 가져오기

rn = 1인 레코드만 선택하여 각 음식 종류별로 가장 FAVORITES가 높은 레스토랑만 가져옴.
```
```
✅ 이 방법의 장점
1. 명확한 그룹화: ROW_NUMBER()는 각 FOOD_TYPE 별로 FAVORITES가 가장 높은 레스토랑을 효율적으로 찾을 수 있게 함. ROW_NUMBER()는 PARTITION BY를 사용하여 특정 기준으로 그룹을 나눈 후 각 그룹 내에서 순위를 지정하므로, 중첩 서브쿼리와 비교해 코드의 흐름이 명확해짐.

2. 성능 향상: 중첩 서브쿼리를 사용하는 방식에서는 MAX(FAVORITES) 조건에 맞는 행을 찾기 위해 같은 테이블에 여러 번 접근해야 함. 반면, ROW_NUMBER() 방식에서는 테이블에 한 번만 접근하여 순위를 매기기 때문에 성능이 향상될 수 있음.

3. 확장성 및 유지 보수 용이성: WITH 절(CTE)은 쿼리를 논리적 단계로 나누어 읽기 쉽게함. 이 방식은 쿼리의 일부를 변경해야 할 때 더 쉽게 수정할 수 있어 유지 보수가 편리함.

4. 다중 정렬 기준: ROW_NUMBER()를 사용할 경우, ORDER BY FAVORITES DESC, REST_ID처럼 다중 정렬 기준을 설정할 수 있음. MAX() 함수와는 달리, 순위가 같은 경우 특정 열을 기준으로 추가 정렬을 할 수 있음.

따라서, WITH 절과 ROW_NUMBER()를 사용하는 방식은 중첩 쿼리보다 가독성, 성능, 확장성에서 모두 유리함.
```
**RANK() 또는 DENSE_RANK()를 사용한 쿼리**
```sql
WITH RankedRestaurants AS (
    SELECT 
        FOOD_TYPE, 
        REST_ID, 
        REST_NAME, 
        FAVORITES, 
        RANK() OVER (PARTITION BY FOOD_TYPE ORDER BY FAVORITES DESC) AS rank
    FROM REST_INFO
)
SELECT 
    FOOD_TYPE, 
    REST_ID, 
    REST_NAME, 
    FAVORITES
FROM RankedRestaurants
WHERE rank = 1
ORDER BY FOOD_TYPE DESC;
```
```
RANK() 또는 DENSE_RANK()를 사용하면 동일한 FAVORITES 값을 가진 레스토랑을 모두 가져올 수 있다.
```
### RANK, DENSE_RANK, ROW_NUMBER 비교
1. RANK
![img](whatiddone/SQL_Study/2025_Winter_Study/image/RANK.png)
2. DENSE_RANK
![img](whatiddone/SQL_Study/2025_Winter_Study/image/DENSE_RANK.png)
3. ROW_NUMBER
![img](whatiddone/SQL_Study/2025_Winter_Study/image/ROW_NUMBER.png)

| 함수명         | 동일한 값이 있을 때 | 순위 건너뛰기 여부 | 유일한 순위 보장 |
|--------------|-----------------|----------------|--------------|
| `ROW_NUMBER()`  | 동일한 값이어도 각 행에 다른 번호 부여 | O (고유한 값으로 부여됨) | O |
| `RANK()`       | 동일한 값이면 같은 순위 부여 | O (다음 순위 건너뜀, 예: 1, 1, 3) | X |
| `DENSE_RANK()` | 동일한 값이면 같은 순위 부여 | X (연속된 순위 유지, 예: 1, 1, 2) | X |

🔥 **차이점 요약**
- `ROW_NUMBER()`: 동일한 값이라도 **순차적으로 고유한 번호를 부여**함.
- `RANK()`: 동일한 값이면 같은 순위이지만 **다음 순위는 건너뜀**.
- `DENSE_RANK()`: 동일한 값이면 같은 순위이지만 **순위가 연속됨**.

💡 **사용 예시**
- **`ROW_NUMBER()`**: 동일한 값이라도 **무조건 한 개의 행만 가져와야 할 때**.
- **`RANK()`**: 공동 순위를 반영하되 **다음 순위는 건너뛰는 방식**이 필요한 경우.
- **`DENSE_RANK()`**: 공동 순위를 반영하되 **순위를 연속적으로 유지해야 할 때**.


- RANK(), DENSE_RANK(), ROW_NUMBER()의 선택은 중복된 값이 있을 때 **순위 부여 방식**이 중요한 경우와 **순위의 연속성을 유지**할 필요가 있는지에 따라 달라짐.


# 7주차
## [ISNULL] NULL처리하기 (SQL 고득점kit)
```
입양 게시판에 동물 정보를 게시하려 합니다. 동물의 생물 종, 이름, 성별 및 중성화 여부를 아이디 순으로 조회하는 SQL문을 작성해주세요. 이때 프로그래밍을 모르는 사람들은 NULL이라는 기호를 모르기 때문에, 이름이 없는 동물의 이름은 "No name"으로 표시해 주세요.
```
https://school.programmers.co.kr/learn/courses/30/lessons/59410
### 문제1. IFNULL()으로 해결
```sql
SELECT 
    ANIMAL_TYPE, 
    IFNULL(NAME, 'No name') as NAME, 
    SEX_UPON_INTAKE
FROM ANIMAL_INS
```
### 문제2. CASE WHEN으로 해결
```sql
SELECT 
    ANIMAL_TYPE, 
    CASE WHEN NAME IS NULL THEN "No name"
    ELSE NAME
    END AS NAME,
    SEX_UPON_INTAKE
FROM ANIMAL_INS
```

### 문제 3. 문제를 풀어주세요 (힌트: IF, LIKE를 사용할 수 있습니다) https://school.programmers.co.kr/learn/courses/30/lessons/59409#qna
```sql
SELECT 
    ANIMAL_ID, 
    NAME, 
    NEUTERED_STATUS
FROM (
    SELECT 
        ANIMAL_ID, 
        NAME, 
        CASE 
            WHEN SEX_UPON_INTAKE LIKE '%NEUTERED%' OR SEX_UPON_INTAKE LIKE '%SPAYED%' 
            THEN 'O'
            ELSE 'X'
        END AS NEUTERED_STATUS
    FROM ANIMAL_INS
) AS subquery
ORDER BY ANIMAL_ID;
```
- IF, LIKE 사용하기
```sql
SELECT 
    ANIMAL_ID,
    NAME,
    IF(SEX_UPON_INTAKE LIKE '%Neutered%' OR SEX_UPON_INTAKE LIKE '%Spayed%', 'O', 'X') AS NEUTERED_STATUS
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```
- IF()
```sql
IF(조건, 참일 때 반환값, 거짓일 때 반환값)
```

- LIKE
```sql
컬럼명 LIKE '패턴'
```
    - LIKE 연산자는 특정 패턴을 가진 문자열을 검색할 때 사용
    - % → 0개 이상의 문자와 일치
    - _ → 정확히 1개의 문자와 일치
    - NOT LIKE: 특정 단어가 없는 데이터 찾기:'NOT LIKE '%John%'' → 이름에 "John"이 없는 데이터 찾기

### 문제 4. 아래는 QnA에 올라온 질문입니다. 왜 풀이가 틀렸는지 답해주세요. 
```sql
SELECT 
    animal_id, 
    name,
    if (sex_upon_intake like '%Neutered%' or '%Spayed%', 'O' , 'X') as '중성화'
from animal_ins
order by 1
```
- IF 구문의 별칭을 지정해주지 않았다.
- '%Spayed%'는 컬럼명이 없고, 독립적으로 OR 연산자에 연결되어 있어서 오류가 발생한다.
- AS '중성화' 컬럼명 오류 
    - MySQL에서는 작은따옴표(')를 컬럼명에 사용할 수 없음.
    - 컬럼명은 백틱(`) 또는 큰따옴표(")로 감싸야 함.
    - 또는 그냥 공백 없이 as 중성화로 사용 가능.


# 수린문제
## 문제 1
### JOIN/ 있었는데요 없었습니다.(https://school.programmers.co.kr/learn/courses/30/lessons/59043)
```sql
SELECT
    AI.ANIMAL_ID,
    AI.NAME
FROM ANIMAL_INS AS AI
JOIN ANIMAL_OUTS AS AO
ON AI.ANIMAL_ID = AO.ANIMAL_ID
WHERE AI.DATETIME > AO.DATETIME
ORDER BY ai.datetime;
```
- IN과 OUT은 SQL의 예약어이므로 테이블 별칭으로 사용할 수 없다.

## 문제 2
### GROUP BY/고양이와 개는 몇 마리 있을까(http://school.programmers.co.kr/learn/courses/30/lessons/59040)
```sql
SELECT
    ANIMAL_TYPE,
    count(ANIMAL_TYPE) as count
FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
ORDER BY ANIMAL_TYPE ASC;
```
## 문제 3
### SELECT/ 특정 세대의 대장균 찾기(https://school.programmers.co.kr/learn/courses/30/lessons/301650)
```sql
SELECT 
    A.ID
FROM ECOLI_DATA A
JOIN ECOLI_DATA B
ON A.PARENT_ID = B.ID
JOIN ECOLI_DATA C
ON B.PARENT_ID = C.ID
WHERE ISNULL(C.PARENT_ID)
ORDER BY ID;
```
- 조건을 걸어서 세대 수를 카운팅 할 수는 없을까
```

```
## 문제 4
### SUBQUERY/폐쇄할 따릉이 정류소 찾기 2(https://solvesql.com/problems/find-unnecessary-station-2/)
```sql
```