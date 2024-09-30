> # 2-6. 연습문제 1~3번
---
### 1. 포켓몬 중에 type2가 없는 포켓몬의 수를 작성하는 쿼리를 작성해주세요
#### 풀이
* '수'를 작성 -> count 함수 사용하기 -> count는 집계 함수 -> GROUP BY 필요!
* type 2가 없는 -> 조건 -> WHERE
```
SELECT
  type2,
  count(id) as CNT
FROM basic.pokemon
WHERE
 type2 IS NULL
GROUP BY type2;
```

#### 설명

- 조건: type2가 없는
  - type2가 어떻게 생겼지?(문자열) type2의 정의는 무엇이지?
- NULL은 뭘까?
  - 아무것도 없는 값. 값이 존재하지 않을 때 NULL
  - NULL은 0이랑 다르고, ""과도 다름 -> 값이 없는 상태
  - type2 = NULL은 안되나? -> X, NULL은 다른 값과 직접 비교할 수 없음. 'NULL = NULL'은 거짓이 아니라 알 수 없음
  - 핵심 : NULL은 IS 연산자를 사용한다!
  - NULL 값만 빼고 로드하려면 '컬럼이름' IS NOT NULL
- 어떤 테이블: pokemon
- 어떤 컬럼: 따로 없음, 포켓몬의 수만 남기면 됨.
- 어떻게 집계: 포켓몬의 수 -> COUNT

#### 정답
```
SELECT
  COUNT(id) AS cnt
FROM basic.pokemon
WHERE
 type2 IS NULL;
```
- WHERE 절에서 여러 조건을 연결하고 싶은 경우 -> AND 조건을 사용
  - WHERE<br> type2 IS NULL<br> AND type1 = "Fire" <br>-> type2가 NULL인 값과 type1이 Fire인 값을 연결
- OR 조건 -> (A) OR (B): A 또는 B

![1번문제](/Basic%20Assignment/3rd_week_img/Q1_1.png)

---
### 2. type2가 없는 포켓몬의 type1과 type1의 포켓몬 수를 알려주는 쿼리를 작성해주세요. 단, type1의 포켓몬 수가 큰 순으로 정렬해주세요.
#### 풀이
* 보여줘야 하는 거: type1, type1의 포켓몬 수
* 포켓몬의 수 -> count 함수 -> GROUP BY
* 정렬 -> ORDER BY
```
SELECT
 type1,
 count(id) AS cnt
FROM basic. pokemon
WHERE 
  type2 IS NULL
GROUP BY
  type1
ORDER BY 
 cnt DESC;
```

#### 설명
- 테이블: pokemon
- 조건: type2가 없는 포켓몬
- 컬럼: type1
- 집계: 포켓몬 수 -> COUNT
- 정렬: type1의 포켓몬 수가 큰 순으로 정렬 -> ORDER BY, 큰 순으로: 내림차순(DESC) -> ORDER BY 포켓몬 수 DESC

#### 정답
```
SELECT
 type1,
 count(id) AS pokemon_cnt
 -- 집계 함수는 GROUP BY와 같이 다님. 집계하는 기준(컬럼)이 없으면 COUNT만 쓸 수 있으나, 집계하는 기준이 있다면 그 기준 컬럼을 GROUP BY에 써줘야 한다.
FROM basic. pokemon
WHERE 
  type2 IS NULL
GROUP BY
  type1
ORDER BY 
 pokemon_cnt DESC;
```
![2번문제](/Basic%20Assignment/3rd_week_img/Q2_2.png)

### 3. type2 상관없이 type1의 포켓몬 수를 알 수 있는 쿼리를 작성해주세요.
#### 풀이
* 보여줘야 하는 거: type1의 포켓몬 수
* 조건: type2 상관없이
* 포켓몬의 수 -> count 함수 -> GROUP BY
```
SELECT
 type1,
 count(id) as cnt
FROM basic.pokemon
GROUP BY
 type1;
```

#### 설명
- 테이블: pokemon
- 조건: type2 상관없이 -> 조건인가? 아닌가? (한번 꼰 건가?) -? 조건이 아님
- 컬럼: type1
- 집계: 포켓몬 수 -> count

#### 정답
```
SELECT
 type1,
 count(id) as cnt
FROM basic.pokemon
GROUP BY
 type1;
```
![3번문제](/Basic%20Assignment/3rd_week_img/Q3_1.png)

### 4. 전설 여부에 따른 포켓몬 수를 알 수 있는 쿼리를 작성해주세요.
#### 풀이
* 보여줘야 하는 거: 포켓몬 수
* 조건: 전설 여부
* 포켓몬 수 -> count 함수 -> GROUP BY
```
SELECT
 count(id) as pokemon_cnt
FROM basic. pokemon
WHERE 
 ??
GROUP BY
 is_legendary;
```

#### 설명
- 테이블: pokemon
- 조건: ~~전설 여부 -> 컬럼~~ 없음
- 컬럼: 전설(is_legendary)
- 집계: 포켓몬 수 
#### 정답
```
SELECT
 is_legendary,
 count(id) as pokemon_cnt
FROM basic. pokemon
GROUP BY
 is_legendary;
```
![4번문제](/Basic%20Assignment/3rd_week_img/Q4.png)

- 팁(쿼리 빨리 작성하는 법)
  - GROUP BY/ORDER BY 1,2,3... : SELECT의 1,2,3번째 컬럼을 의미
  - 예) SELECT id 일 때, GROUP BY id = GROUP BY 1
  - 그러나 완성된 쿼리문에서는 가독성을 위해 명확하게 컬럼을 명시하기
### 5. 동명 이인이 있는 이름은 무엇일까요? (한번에 찾으려고 하지 않고 단계적으로 가도 괜찮아요)
#### 풀이
* 보여줘야하는 것: 이름 -> 트레이너 테이블
* 조건: 동명이인: 먼저 이름들을 GROUP BY 해서 2개 이상인 값을 찾아야 하니깐 HAVING 사용.
```
SELECT
  name,
  count(name) as cnt_name
FROM basic.trainer
GROUP BY
 name
HAVING
 cnt_name >=2;
```
#### 설명
- 테이블: trainer
- 조건: 같은 이름이 2개 이상(동명이인) -> COUNT(name) -> 2개 이상
- 컬럼: 이름
- 집계: COUNT 
#### 정답
```
SELECT
  name,
  COUNT(name) AS trainer_cnt
FROM basic.trainer
GROUP BY
 name
HAVING
 trainer_cnt >=2;
```
- 집계 후 조건 -> HAVING
- FROM 절의 테이블 조건 -> WHERE
```
# WHERE 절(+서브쿼리)을 사용해 문제 해결
SELECT
 *
FROM (
  SELECT
    name,
    COUNT(name) AS trainer_cnt
  FROM basic.trainer
  GROUP BY
    name
)
WHERE
  trainer_cnt >=2;
 ```
![5번문제](/Basic%20Assignment/3rd_week_img/Q5.png)

### 6. trainer 테이블에서 "Iris"트레이너의 정보를 알 수 있는 쿼리를 작성해주세요.
#### 풀이
* 보여줘야하는 것: 트레이너의 정보
* 조건: 이름이 Iris
```
SELECT
  *
FROM basic.trainer
WHERE
 name = 'Iris';
```
#### 설명
- 테이블: trainer
- 조건: 트레이너의 이름 = 'Iris'
- 컬럼: 정보 -> 모든 컬럼
- 집계: X

#### 정답
```
SELECT
  *
FROM basic.trainer
WHERE
 name = 'Iris';
```
![6번문제](/Basic%20Assignment/3rd_week_img/Q6.png)

### 7. trainer 테이블에서 "Iris", "Whitney", "Cynthia" 트레이너의 정보를 알 수 있는 쿼리를 작성해주세요.
#### 풀이
* 보여줘야하는 것: 트레이너들의 정보
* 조건: 이름= "Iris", "Whitney", "Cynthia" -> OR 사용
```
SELECT
  *
FROM basic.trainer
WHERE
 (name = "Iris") OR (name = "Whitney") OR (name = "Cynthia");
```
#### 설명
- 테이블: trainer
- 조건: 트레이너의 이름 = "Iris", "Whitney", "Cynthia" 중에 있으면 추출
- 컬럼: 정보 -> 모든 컬럼
- 집계: X
#### 정답
```
SELECT
  *
FROM basic.trainer
WHERE
 (name = "Iris") 
 OR (name = "Whitney") 
 OR (name = "Cynthia");
```
![7번문제](/Basic%20Assignment/3rd_week_img/Q7.png)
```
# IN을 이용해 간단하게 만들기
SELECT
  *
FROM basic.trainer
WHERE
 name IN("Iris", "Whitney", "Cynthia");
*IN-> name에 괄호 안의 Value가 있는 Row만 추출
```

### 8. 전체 포켓몬 수는 얼마나 되나요?
#### 풀이
* 보여줘야하는 것: 전체 포켓몬 수
* 포켓몬 수: count 함수 -> GROUP BY(이번에는 데이터 묶는 건 없어서 불필요)
```
SELECT
 count(id) AS cnt_pokemon
FROM basic.pokemon;
```
#### 설명
- 테이블: pokemon
- 조건: X
- 컬럼: X
- 집계: 포켓몬 수 -> COUNT(id)
#### 정답
```
SELECT
 count(id) AS cnt_pokemon
FROM basic.pokemon;
```
![8번문제](/Basic%20Assignment/3rd_week_img/Q8.png)

### 9. 세대(generation)별로 포켓몬 수가 얼마나 되는지 알 수 있는 쿼리를 작성해주세요.
#### 풀이
* 보여줘야하는 것: 포켓몬 수
* 집계: 세대
* 포켓몬 수: count 함수 -> GROUP BY(세대)
```
SELECT
 generation,
 count(id) AS cnt_generation
FROM basic.pokemon
GROUP BY
 generation;
```
#### 설명
- 테이블: pokemon
- 조건: X
- 컬럼: 세대(generation)
- 집계: 포켓몬 수 -> COUNT(id)
#### 정답
```
SELECT
 generation,
 count(id) AS cnt_generation
FROM basic.pokemon
GROUP BY
 generation;
```
![9번문제](/Basic%20Assignment/3rd_week_img/Q9.png)

### 10. type2가 존재하는 포켓몬의 수는 얼마나 되나요?
#### 풀이
* 보여줘야하는 것: 포켓몬 수
* 조건: type2가 존재(null값이 아님)
* 포켓몬 수: count 함수 -> GROUP BY
```
SELECT
 count(id) as pokemon_cnt
FROM basic.pokemon
WHERE
 type2 IS NOT NULL
```
#### 설명
- 테이블: pokemon
- 조건: type2가 존재 -> IS NOT NULL
- 컬럼: 세대(generation)
- 집계: 포켓몬 수 -> COUNT(id)
#### 정답
```
SELECT
 count(id) as pokemon_cnt
FROM basic.pokemon
WHERE
 type2 IS NOT NULL
```
![10번문제](/Basic%20Assignment/3rd_week_img/Q10.png)

### 11. type2가 있는 포켓몬 중에 제일 많은 type1은 무엇인가요?
#### 풀이
* 보여줘야하는 것: type1
* 조건: type2가 존재(null값이 아님)
```
SELECT
FROM basic.pokemon
```
#### 설명
- 테이블: pokemon
- 조건: type2가 존재 -> IS NOT NULL
- 컬럼: 세대(generation)
- 집계: 포켓몬 수 -> COUNT(id)
#### 정답

### 12. 단일(하나의 타입만 있는) 포켓몬 중 가장 많은 type1은 무엇일까요?
#### 풀이
* 보여줘야하는 것: type1
* 조건: type1은 존재하고 type2가 null AND type1이 null이고 type2가 존재
#### 설명
#### 정답

### 13. 포켓몬의 이름에 "파"가 들어가는 포켓몬은 어떤 포켓몬이 있을까요?
#### 풀이
* 보여줘야하는 것: 포켓몬 이름
* 조건: 이름에 '파'
#### 설명
#### 정답

### 14. 뱃지가 6개 이상인 트레이너는 몇 명이 있나요?
#### 풀이
* 보여줘야하는 것: 트레이너 수
* 조건: 뱃지 >= 6
* 트레이너 수: count 함수 -> GROUP BY
#### 설명
#### 정답

### 15. 트레이너가 보유한 포켓몬(trainer_pokemon)이 제일 많은 트레이너는 누구일까요?
#### 풀이
* 보여줘야하는 것: 트레이너 이름
* 조건: trainer_pokemon이 max
* MAX: 집계함수 -> GROUP BY
#### 설명
#### 정답

### 16. 포켓몬을 가장 많이 풀어준 트레이너는 누구일까요?
#### 풀이
* 보여줘야하는 것: 트레이너 이름
* 조건: 풀어준 포켓몬이 max
* MAX: 집계함수 -> GROUP BY
#### 설명
#### 정답

### 17. 트레이너 별로 풀어준 포켓몬의 비율이 20%가 넘은 포켓몬 트레이너는 누구일까요? 풀어준 포켓몬의 비율=(풀어준 포켓몬 수/전체 포켓몬의 수)
#### 풀이
* 보여줘야하는 것: 트레이너 이름
* 조건: 풀어준 포켓몬의 비율 >= 20%
#### 설명
#### 정답
