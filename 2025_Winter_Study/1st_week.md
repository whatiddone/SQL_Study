# 2주차
## 문제 1
### 우리 플랫폼에 정착한 판매자 1

- 코드 1
```sql
SELECT
  seller_id
  COUNT(order_id) as orders
FROM olist_order_items_dataset
WHERE
  orders >= 100
GROUP BY seller_id;
```
WHERE은 집계 전 필터링 <br> HAVING은 집계 후 필터링 <br> 집계함수인 'COUNT'를 사용해서 집계를 이미 하였으므로 HAVING을 사용해야 한다.

- 코드 2
```sql
SELECT
  seller_id,
  COUNT(order_id) AS orders
FROM olist_order_items_dataset
GROUP BY seller_id
HAVING orders >= 100;
```
쿼리는 실행되나 DISTINCT를 사용하지 않아 중복값이 제거되지 않음

- 정답
```sql
SELECT
  seller_id,
  COUNT(DISTINCT order_id) AS orders
FROM olist_order_items_dataset
GROUP BY seller_id
HAVING orders >= 100;
```

## 문제 2
### 몇 분이서 오셨어요?

- 정답
```sql
SELECT *
FROM tips
WHERE size % 2>0
```
%는 나머지를 구하는 연산자

## 문제 3
### 최고의 근무일을 찾아라

- 정답
```sql
SELECT
  day,
  round(sum(tip),3) as tip_daily
FROM tips
GROUP BY 
  day
ORDER BY 
  tip_daily DESC
LIMIT 1;
```
ROUND(number, decimal_places) <br> 주요 특징:
소수점 자릿수를 지정하지 않으면 정수로 반올림합니다. <br>
decimal_places가 양수이면 소수점 오른쪽의 자릿수까지 반올림합니다. <br>
decimal_places가 음수이면 소수점 왼쪽의 자릿수를 기준으로 반올림합니다.

# 3주차
## 문제 1
### 조건에 맞는 사용자와 총 거래금액 조회하기
```sql
SELECT 
  U.USER_ID, 
  U.NICKNAME, 
  SUM(B.PRICE) AS TOTAL_SALES
FROM USED_GOODS_BOARD AS B
JOIN USED_GOODS_USER AS U
ON U.USER_ID = B.WRITER_ID
WHERE 
  B.STATUS = 'DONE' 
GROUP BY 
  B.WRITER_ID
HAVING 
  SUM(B.PRICE) >= 700000
ORDER BY 
  TOTAL_SALES ASC;
```

## 문제 2
### 업그레이드 할 수 없는 아이템 구하기
```sql
SELECT
 I.ITEM_ID, 
 I.ITEM_NAME, 
 I.RARITY
FROM ITEM_INFO AS I
LEFT JOIN ITEM_TREE AS T
ON I.ITEM_ID=T.ITEM_ID
WHERE T.PARENT_ITEM_ID='NULL'
ORDER BY I.ITEM_ID DESC;
```
-실패..

## 문제 3
### 조건에 맞는 개발자 찾기
```sql
- by GPT...
SELECT 
    ID, 
    EMAIL, 
    FIRST_NAME, 
    LAST_NAME
FROM 
    DEVELOPERS
WHERE 
    SKILL_CODE & (256 + 1024) > 0
ORDER BY 
    ID ASC;
```
- & 연산이 **비트연산**을 수행하는 연산자라는 것을 알아야 한다.
```
& 연산자는 비트 단위에서 논리적 AND 연산을 수행합니다.
두 숫자를 이진수로 변환한 뒤, 각 자리의 비트를 비교하여 둘 다 1인 경우에만 1을 반환합니다.
즉, 비트 단위에서 겹치는 부분만 "남긴다"고 생각하면 됩니다.
```

- & 연산과 AND 연산의 차이
  - & 연산: 1 & 0 (비트 연산)
&는 **비트 단위**에서 AND 연산을 수행합니다.
이 경우, 숫자를 이진수로 변환한 뒤 각 비트를 비교합니다.(**숫자비교**)
  - 1 AND 0 (논리 연산)
AND는 논리 연산자로, **Boolean 값(참/거짓)**을 비교합니다.
이 경우, **True(1)**와 **False(0)**를 비교하여 결과를 반환합니다.

- | 연산과 OR 연산의 차이
  - | 연산: 1 & 0 (비트 연산)
|는 **비트 단위**에서 OR 연산을 수행합니다.
이 경우, 숫자를 이진수로 변환한 뒤 각 비트를 비교합니다.(**숫자비교**)
```
5  =  0101
3  =  0011
--------------
|    0111  (결과 = 7)
```
  - 1 OR 0 (논리 연산)
OR 논리 연산자로, **Boolean 값(참/거짓)**을 비교합니다.
이 경우, **True(1)**와 **False(0)**를 비교하여 결과를 반환합니다.
```
TRUE OR FALSE → TRUE
```