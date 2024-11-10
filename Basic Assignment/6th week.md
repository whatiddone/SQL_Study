># 5-1. Intro
- JOIN: 다량의 자료를 연결: VLOOKUP 함수와 유사

># 5-2. JOIN 이해하기
- "공통 Key로 서로 다른 데이터 테이블을 연결하는 것"
- 테이블을 연결하는 Key Column이 필요(보통 id 값을 Key로 많이 사용)
- JOIN을 해야하는 이유: 데이터가 저장되는 형테에 대한 이해
  - 관계형 데이터베이스(RDBMS) 설계시 정규화 과정을 거침.
    - 정규화는 중복을 최소화하게 데이터를 구조화 <br>
     예) User Table은 유저 데이터만, Order Table은 주문 데이터만 
    - 따라서 데이터를 다양한 테이블에 저장하고, 필요할 때 JOIN하여 사용.

># 5-3. 다양한 JOIN 방법
- (INNER) JOIN: 두 테이블의 **공통** 요소만 연결
- LEFT/RIGHT (OUTER) JOIN: **왼쪽/오른쪽 테이블 기준**으로 연결
- FULL (OUTER) JOIN: **양쪽 기준**으로 연결, 없는 요소는 NULL값
- CROSS JOIN: 두 테이블의 각각의 **요소를 곱하기**
![img](/Basic%20Assignment/img/6th_week_img/1.png)
![img](/Basic%20Assignment/img/6th_week_img/2.png)

># 5-4. JOIN 쿼리 작성하기
- SQL JOIN 쿼리 작성하는 흐름
  1. 테이블 확인: 테이블에 저장된 데이터, 컬럼 확인
  2. 기준 테이블 정의: 가장 많이 참고할 기준(base) 테이블 정의
  3. JOIN Key 찾기: 여러 테이블과 연결할 Key(ON) 정리
  4. 결과 예상하기: 결과 테이블을 예상해서 손, 엑셀로 작성
  5. 쿼리 작성/검증: 예상한 결과와 동일한 결과가 나오는지 확인

```sql
# FROM 하단에 JOIN 할 Table을 작성하고 ON 뒤에 공통된 컬럼(Key)를 작성

SELECT
  A.col1,
  A.col2,
  B.col11,
  B.col12
FROM table1 AS A    # 테이블 이름에 Alias(별칭)를 사용 가능
LEFT JOIN table2 AS B
ON A.key = B.Key 
```
- JOIN의 핵심 문법: FROM, (INNER/LEFT/RIGHT/FULL)JOIN, ON A.key=B.key
![img](/Basic%20Assignment/img/6th_week_img/3.png)

```sql
# trainer 테이블이랑 trainer.pokemon 테이블 조인
SELECT
  tp.*,
  t.*
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id=t.id;
```
![img](/Basic%20Assignment/img/6th_week_img/4.png)

```sql
# trainer 테이블이랑 trainer.pokemon 테이블이랑 pokemon 테이블 조인
SELECT
  tp.*,
  t.*,
  p.*
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id=t.id
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id=p.id;
```
![img](/Basic%20Assignment/img/6th_week_img/5.png)

```sql
# LEFT : trainer_pokemon
# RIGHT : trainer
# RIGHT : pokomon

SELECT
 tp.*,
 t.* EXCEPT(id), # tp에 trainer_id가 있으니 중복 방지
 p.* EXCEPT(id) # tp에 pokemon_id가 있으니 중복 방지
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
# ON: Join KEY를 기입
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
```
![img](/Basic%20Assignment/img/6th_week_img/6.png)

># 5-5. JOIN을 처음 공부할 때 헷갈렸던 부분
1. 여러 JOIN 중 어떤 것을 사용해야 하는가?
- 작업의 목적에 따라 JOIN을 선택
- 교집합: INNER
- 모두 조합: CROSS
- 그 외: LEFT/RIGHT (LEFT 추천)
  - RIGHT 컬럼에 NULL 값이 있는 경우: LEFT JOIN 후 '컬럼' IS NOT NULL 을 하면 INNER 조인과 같은 결과
- 쿼리 작성 템플릿에 예상하는 결과를 작성하고, 중간 결과도 생각하면서 찾아보기. (단계적으로)

2. 어떤 Table을 왼쪽에 두고, 어떤 Table을 오른쪽에 두어야 하는가?
- LEFT JOIN의 경우
  - 기준이 되는 테이블(기준값이 존재하는 테이블)을 왼쪽에 두기
    - 기준값: 데이터 요소가 빠짐없이 존재하는 값
  - 필요한 데이터는 기준값의 우측에 계속 추가
  - 예) 주문한 유저: Order ← User
  - 예) 주문을 안 한 유저: User ← Order 에서 Null 값만 추출 
  - 풀어야 하는 문제의 **기준**을 생각

3. 여러 테이블을 연결할 수 있는가?
- JOIN 개수에 한계는 없지만, 적당히(3~5개 정도) JOIN 하기
  - 6개를 한번에 조인하지 말고, 3개씩 나눠서 큰 JOIN 2개를 JOIN 하자

4. 모든 컬럼을 선택해야 하는가?
- 데이터 추출로 어떤 일을 하고자 하는지에 따라 다름
- id 같은 값은 Unique한지 확인하기 위해 자주 사용

5. NULL이 무엇인가?
- NULL: 값이 없음, 알 수 없음
- 0, 공백과는 다르게 값이 아예 없는 것
- JOIN에서 연결할 값이 없는 경우, NULL값이 나타남

># 5-6. JOIN 연습문제
### 1. 트레이너가 보유한 포켓몬들은 얼마나 있는지 알 수 있는 쿼리를 작성해주세요.(보유: status가 Active, Training, Released는 방출)
#### 풀이
```sql
SELECT
  p.kor_name,
  count(tp.id) as pokemon_cnt
FROM (
  SELECT
    id,
    trainer_id,
    pokemon_id 
  FROM basic.trainer_pokemon 
  where 
    status='active' AND 'training'
  ) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id=p.id
GROUP BY p.kor_name;
```
![img](/Basic%20Assignment/img/6th_week_img/7.png)

```sql
SELECT
  p.kor_name,
  COUNT(tp.id) AS pokemon_cnt
FROM (
  SELECT
    id,
    trainer_id,
    pokemon_id 
  FROM basic.trainer_pokemon 
  WHERE status IN ('active', 'training')
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
GROUP BY p.kor_name;
```
![img](/Basic%20Assignment/img/6th_week_img/8.png)
...?: 데이터 대소문자 확인하기!!

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 : 포켓몬들이(이름 명시) 얼마나 있는지 알고 싶다. 포켓몬의 수
# 쿼리 계산 방법 : trainer_pokemon + pokemon JOIN -> 그 후에 GROUP BY 집계 (COUNT)
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon + pokemon
# Join KEY : trainer_pokemon.pokemon_id + pokemon.id
# 데이터 특징: 
-- 보유했다의 정의는 status가 active, training
-- released는 방출

# 1) trainer_pokemon에서 status가 Active, Training인 경우만 필터링(WHERE)
  -- 1)을 먼저 하는 것이 좋을까? 혹은 JOIN 이후 Active와 Training을 필터링하는 것이 좋을까?
  -- 연산량 관점에서 먼저 필터링 하는 것이 효율적
# 2) 필터링한 결과를 pokemon Table과 JOIN

SELECT
  tp.*,
  p.kor_name
FROM(
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic. trainer_pokemon
  WHERE
    status IN ("Active","Training")
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id;

# 3) 2)의 결과에서 pokemon_name, COUNT(pokemon_id) AS pokemon_cnt
```
#### 정답
```sql
SELECT
  p.kor_name,
  count(tp.id) AS pokemon_cnt
FROM(
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic. trainer_pokemon
  WHERE
    status IN ("Active","Training")
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
GROUP BY
 kor_name
ORDER BY
 pokemon_cnt DESC;
```
![img](/Basic%20Assignment/img/6th_week_img/9.png)

- 순서: SELECT FROM (JOIN-ON) WHERE GROUP BY
- WHERE 1=1: 무조건 TRUE를 반환 -> 모든 ROW를 출력

```
SELECT
  p.kor_name,
  count(tp.id) AS pokemon_cnt
FROM(
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic. trainer_pokemon
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE
 1=1
 OR status = "Active"
 OR status = "Training"
GROUP BY
 kor_name
ORDER BY
 pokemon_cnt DESC;
```
![img](/Basic%20Assignment/img/6th_week_img/9.png)


### 2. 각 트레이너가 가진 포켓몬 중 'Grass' 타입의 포켓몬 수를 계산해주세요. (단, 편의를 위해 type1 기준으로 계산해주세요.)
#### 풀이
```sql
SELECT
  tp.trainer_id,
  p.type1,
  count(p.id) AS grass_cnt
FROM(
  SELECT
  id,
  trainer_id,
  pokemon_id,
  status
FROM basic.trainer_pokemon
WHERE status IN ('Active', 'Training')
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE 
  p.type1="Grass"

GROUP BY
 tp.trainer_id, p.type1

ORDER BY
 grass_cnt DESC;
```
![img](/Basic%20Assignment/img/6th_week_img/10.png)

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 : 트레이너가 가지고 있는 포켓몬 중 Grass type의 수
# 쿼리 계산 방법 : 트레이너가 보유한 포켓몬 조건 설정 => Grass 타입으로 WHERE 조건 걸어서 COUNT
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon + pokemon
  -- 우리가 풀려고 하는 문제에서 기준이 되는 테이블이 왼쪽
  -- pokemon을 왼쪽에 두면? pokemon 중에 보유되지 않았던 pokemon들은 trainer_pokemon에 없을 것이기 때문에 NULL
  -- trainer_pokemon을 왼쪽에 두면 트레이너가 보유했던 포켓몬들을 기준으로 포켓몬 데이터만 추가. NULL X
  -- JOIN을 할 수 있는 KEY가 많은 테이블을 왼쪽으로
# Join KEY : trainer_pokemon.pokemon_id + pokemon.id
# 데이터 특징: 
-- 타입은 type 1 기준으로
-- 보유했다의 정의는 status가 active, training
-- released는 방출
```

#### 정답
```sql
SELECT
  p.type1,
  count(p.id) AS pokemon_cnt
FROM(
  SELECT
  id,
  trainer_id,
  pokemon_id,
  status
FROM basic.trainer_pokemon
WHERE status IN ('Active', 'Training')
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE 
  p.type1="Grass"
GROUP BY
  p.type1
ORDER BY
 2 DESC;
```
![img](/Basic%20Assignment/img/6th_week_img/11.png)

### 3. 트레이너의 고향(hometown)과 포켓몬을 포획한 위치(location)을 비교하여, 자신의 고향에서 포켓몬을 포획한 트레이너의 수를 계산해주세요. (status와 상관없이 구해주세요.)
#### 풀이
- 트레이너 조건: trainer.hometown=pokemon_trainer.location
- 트레이너 수 집계
```sql
SELECT
  count(t.id) as cnt
FROM basic.trainer as t
LEFT JOIN basic.trainer_pokemon as tp
ON t.id = tp.trainer_id
WHERE
  t.hometown=tp.location;
```
![img](/Basic%20Assignment/img/6th_week_img/12.png)

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 : 트레이너 고향과 포켓몬의 포획 위치가 같은 트레이너의 수 계산
# 쿼리 계산 방법 : trainer(hometown),trainer_pokemon(location) JOIN -> hometown = location => 트레이너의 수 count
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon + trainer
# Join KEY : trainer_pokemon.pokemon_id + trainer.id
# 데이터 특징: 
-- status 상관없이
```
#### 정답
```sql
SELECT
  count(DISTINCT t.id) as cnt
  # count(t.id) as cnt : 트레이너와 포켓몬이 같은 건이 43개
FROM basic.trainer as t
LEFT JOIN basic.trainer_pokemon as tp
ON t.id = tp.trainer_id
WHERE
  t.hometown=tp.location
  and location IS NOT NULL
  -- trainer 중에 포켓몬을 잡아보지 못한 trainer가 있으면 NULL 조건을 걸어줘야 한다. 지금 데이터에는 trainer 테이블에 있는 trainer 들은 모두 포켓몬을 잡아봐서 신경 X
```
![img](/Basic%20Assignment/img/6th_week_img/13.png)

### 4. Master 등급인 트레이너들은 어떤 타입의 포켓몬을 제일 많이 보유하고 있을까요?
LEFT: trainer_pokemon
RIGHT: pokemon&trainer
#### 풀이
```sql
SELECT
SELECT
  p.type1,
  COUNT(tp.id) AS pokemon_cnt,
  COUNT(DISTINCT tp.trainer_id) AS trainer_cnt
FROM (
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic.trainer_pokemon
  WHERE status IN ('Active', 'Training')
) AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE t.achievement_level = 'Master'
GROUP BY
  p.type1
ORDER BY
  pokemon_cnt DESC;
```
![img](/Basic%20Assignment/img/6th_week_img/14.png)

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 : Master 등급의 트레이너들이 가장 많이 보유하고 있는 타입
# 쿼리 계산 방법 : trainer + pokemon + trainer_pokemon => Master 조건 설정(WHERE) => type1 GROUP BY + COUNT
# 데이터의 기간 : X
# 사용할 테이블: trainer, pokemon trainer_pokemon
# Join KEY : trainer.id = trainer_pokemon.trainer_id, pokemon.id = trainer_pokemon.pokemon_id
 -- 2번 나오는 trainer_pokemon을 LEFT
# 데이터 특징: 
-- status: 보유
```
#### 정답
```sql
SELECT
  p.type1,
  COUNT(tp.id) AS pokemon_cnt,
FROM (
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic.trainer_pokemon
  WHERE status IN ('Active', 'Training')
) AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE 
  t.achievement_level = 'Master'
GROUP BY
  p.type1
ORDER BY
  pokemon_cnt DESC;
```
![img](/Basic%20Assignment/img/6th_week_img/15.png)

### 5. Incheon 출신 트레이너들은 1세대, 2세대 포켓몬을 각각 얼마나 보유하고 있나요?

#### 풀이
```sql
SELECT
tp.trainer_id,
generation,
COUNT(tp.id) AS pokemon_cnt,
hometown
FROM(
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic.trainer_pokemon
  WHERE status IN ('Active', 'Training')
) AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE
  hometown = 'Incheon'
GROUP BY
generation,
tp.trainer_id,
hometown
ORDER BY
trainer_id ASC;
```
![img](/Basic%20Assignment/img/6th_week_img/16.png)

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 :Incheon 출신의 트레이너들이 가장 많이 보유하고 있는 포켓몬을 세대구분하기
# 쿼리 계산 방법 : trainer + pokemon + trainer_pokemon => Incheon 조건 설정(WHERE) => 세대로 GROUP BY
# 데이터의 기간 : X
# 사용할 테이블: trainer, pokemon trainer_pokemon
# Join KEY : trainer.id = trainer_pokemon.trainer_id, pokemon.id = trainer_pokemon.pokemon_id
 -- 2번 나오는 trainer_pokemon을 LEFT
# 데이터 특징: 
-- status: 보유
```
#### 정답
```sql
SELECT
generation,
COUNT(tp.id) AS pokemon_cnt
FROM(
  SELECT
    id,
    trainer_id,
    pokemon_id,
    status
  FROM basic.trainer_pokemon
  WHERE status IN ('Active', 'Training')
) AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE
  t.hometown = 'Incheon'
GROUP BY
generation;
```
- 데이터가 바뀌어도 같은 값을 출력하고 싶으면 WHERE 조건을 이용하기
># 5-7. 정리
- JOIN: 여러 TABLE을 연결해야 할 때 사용하는 문법
- Key: 공통적으로 가지고 있는 컬럼

문법 구조
```
SELECT
  t1.col, t2.col2
FROM table1 AS t1
LEFT JOIN table2 AS t2
ON t1.key=t2.key
```

- JOIN 종류: INNER, LEFT/RIGHT, FULL, CROSS

- SQL JOIN 쿼리 작성하는 흐름
1. 테이블 확인: 테이블 에 저장된 데이터, 컬럼 확인
2. 기준 테이블 정의: 가장 많이 참고할 기준 테이블 정의
3. JOIN Key 찾기: 여러 Table과 연결할 Key(ON) 정리
4. 결과 테이블 에상
5. 쿼리 작성/검증