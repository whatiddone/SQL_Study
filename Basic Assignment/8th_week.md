> # 7. 연습문제

### 1. 각 트레이너별로 가진 포켓몬의 평균 레벨을 계산하고, 그 중 평균 레벨이 높은 TOP3 트레이너의 이름과 보유한 포켓몬의 수, 평균 레벨을 출력해주세요.
#### 풀이
```sql
SELECT
  t.name,
  COUNT(tp.pokemon_id) AS pk_cnt,
  AVG(tp.level) AS avg_level
FROM basic.trainer_pokemon as tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
GROUP BY 
  t.name
ORDER BY
  avg_level DESC
LIMIT 3
```
![img](/Basic%20Assignment/img/8th_week_img/1.png)

#### 해설
```
# 쿼리를 작성하는 목표, 확인할 지표 : 트레이너의 이름, 보유한 포켓몬의 수, 평균 레벨
# 쿼리 계산 방법 : 트레이너가 보유한 포켓몬의 포켓몬의 수, 평균 레벨을 계산 + 트레이너 테이블과 연결해서 트레이너의 이름을 출력
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon, trainer
# Join KEY : trainer_pokemon.trainer_id = trainer.id
# 데이터 특징 : trainer_pokemon의 status => Released는 방출
```
#### 정답
```sql
# (1) 트레이너가 보유한 포켓몬의 평균 레벨, 포켓몬의 수
WITH trainer_avg_level AS (
  SELECT
  trainer_id,
  ROUND(AVG(level), 2) AS avg_level,
  COUNT(id) AS pokemon_cnt
FROM basic.trainer_pokemon
WHERE
  status != "Released" # = : 같다. != 같지 않다
GROUP BY
  trainer_id
)

# (2) : (1)에서 만든 테이블 + trainer 테이블을 합쳐서 trainer name 출력
SELECT
  DISTINCT # trainer의 중복방지
  t.name,
  tal.avg_level,
  tal.pokemon_cnt
FROM basic.trainer AS t
LEFT JOIN trainer_avg_level AS tal
ON t.id = tal.trainer_id
ORDER BY
  avg_level DESC
LIMIT 3
```
![img](/Basic%20Assignment/img/8th_week_img/2.png)

### 2. 각 포켓몬 타입1을 기준으로 가장 많이 포획된(방출 여부 상관없음) 포켓몬의 타입1, 포켓몬의 이름과 포획 횟수를 출력해주세요.
#### 풀이
```sql
# 1단계
SELECT
 type1,
 SUM(catch_count) AS all_catch_count
FROM(
  SELECT 
      p.type1,
      p.kor_name AS pokemon_name,
      catch_pokemon.cnt AS catch_count
  FROM 
     basic.pokemon AS p
  JOIN (
    SELECT 
      tp.pokemon_id,
      COUNT(tp.pokemon_id) AS cnt
    FROM 
      basic.trainer_pokemon AS tp
    GROUP BY 
      tp.pokemon_id
) AS catch_pokemon
ON p.id = catch_pokemon.pokemon_id
)
GROUP BY type1
ORDER BY all_catch_count DESC;
```
![img](/Basic%20Assignment/img/8th_week_img/3.png)
- Water type이 가장 많이 잡힌 걸 확인
```
# 2단계
SELECT
 p.type1,
 p.kor_name,
 COUNT(tp.pokemon_id) as cp 
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE p.type1="Water"
GROUP BY p.kor_name, p.type1
ORDER BY cp DESC
```
![img](/Basic%20Assignment/img/8th_week_img/4.png)

#### 해설
```
# 쿼리를 작성하는 목표, 확인할 지표 : 포켓몬 타입1, 포켓몬 이름, 포켓몬의 포획 횟수
# 쿼리 계산 방법 : trainer_pokemon + pokemon type1, kor_name
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon, pokemon
# Join KEY : trainer_pokemon.pokemon_id = pokemon.id
# 데이터 특징 : X
```
#### 정답
```sql
SELECT
  type1,
  kor_name,
  COUNT(tp.id) as cnt
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.pokemon AS p 
ON tp.pokemon_id = p.id
GROUP BY
  type1,
  kor_name
ORDER BY
  cnt DESC
LIMIT 3
```
![img](/Basic%20Assignment/img/8th_week_img/4.png)

### 3. 전설의 포켓몬을 보유한 트레이너들은 전설의 포켓몬과 일반 포켓몬을 얼마나 보유하고 있을까요? (트레이너의 이름을 같이 출력해주세요)
#### 풀이
```sql

```
#### 해설
#### 정답

### 4. 가장 승리가 많은 트레이너 ID, 트레이너의 이름, 승리한 횟수, 보유한 포켓몬의 수, 평균 포켓몬의 레벨을 출력해주세요. 단, 포켓몬의 레벨은 소수점 둘째 자리에서 반올림해주세요. (참고: 반올림 함수 ROUND)
#### 풀이
```sql

```
#### 해설
#### 정답

### 5. 트레이너가 잡았던 포켓몬의 총 공격력(attack)과 방어력(defense)의 합을 계산하고, 이 합이 가장 높은 트레이너를 찾으세요.
#### 풀이
```sql
WITH attack_and_defense AS (
  SELECT
    tp.trainer_id,
    SUM(p.attack+p.defense) AS attack_and_defense
  FROM basic.trainer_pokemon AS tp
  LEFT JOIN basic.pokemon p
  ON tp.pokemon_id=p.id
  GROUP BY
    tp.trainer_id
)

SELECT
  t.name,
  a_d.trainer_id,
  a_d.attack_and_defense
FROM attack_and_defense AS a_d
LEFT JOIN basic.trainer AS t
ON a_d.trainer_id = t.id
ORDER BY attack_and_defense DESC
LIMIT 1
```
#### 해설
```
# 쿼리를 작성하는 목표, 확인할 지표 : 트레이너 포켓몬의 총 (공격+방어) 공방
# 쿼리 계산 방법 : trainer_pokemon => pokemon. attack + defense
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon, pokemon, trainer
# Join KEY : trainer_pokemon.pokemon_id = pokemon.id
# 데이터 특징 : 
```
#### 정답
```sql
WITH total_stats AS (
  SELECT
    tp.trainer_id,
    -- p.attack,
    -- p.defense,
    SUM(p.attack + p.defense) AS total_stat
  FROM basic.trainer_pokemon AS tp
  LEFT JOIN basic.pokemon AS P
  ON tp.pokemon_id = p.id
  GROUP BY
    tp.trainer_id
)

SELECT
  t.name,
  ts.trainer_id,
  ts.total_stat
FROM total_stats AS ts
LEFT JOIN basic.trainer AS t
ON ts.trainer_id = t.id
ORDER BY 
  total_stat DESC
LIMIT 1
```
### 6. 각 포켓몬의 최고 레벨과 최저 레벨을 계산하고, 레벨 차이가 가장 큰 포켓몬의 이름을 출력하세요.
#### 풀이
- 레벨 차이를 계산하고 이름 추가하기
```sql
SELECT
  kor_name,
  level_gap
FROM(
  SELECT
    tp.pokemon_id,
    p.kor_name
    MAX(tp.level)- MIN(tp.level) AS level_gap
  FROM basic.trainer_pokemon AS tp
  LEFT JOIN basic.pokemon AS p
  ON tp.pokemon_id = p.id
  GROUP BY
    tp.pokemon_id,
    p.kor_name
)
ORDER BY
  level_gap DESC
LIMIT 1

```
#### 해설
```
# 쿼리를 작성하는 목표, 확인할 지표 : 포켓몬의 레벨 차이(최고 레벨 - 최저 레벨)
# 쿼리 계산 방법 : trainer_pokemon에서 포켓몬의 최고 레벨, 최저 레벨을 계산 -> 차이를 구하고 -> 차이가 큰 순으로 정렬
# 데이터의 기간 : X
# 사용할 테이블: trainer_pokemon, pokemon
# Join KEY : traerin_pokemon.pokemon_id = pokemon_id
# 데이터 특징 : X
```

#### 정답
``` sql
# (1) 포켓몬의 레벨 차이를 구하는 쿼리
WITH level_diff AS (
  SELECT
    tp.pokemon_id, 
    p.kor_name,
    MIN(tp.level) AS min_level,
    MAX(tp.level) AS max_level,
    MAX(tp.level) - MIN(tp.level) AS level_difference
  FROM basic.trainer_pokemon AS tp
  LEFT JOIN basic.pokemon AS p
  ON tp.pokemon_id = p.id
  GROUP BY
    tp.pokemon_id,
    p.kor_name
)
# pokemon_id => min level : 6, max level : 22. level_difference = 16
# 12 | 16
# min level => MIN
# max level => MAX

SELECT
  kor_name,
  level_difference
FROM level_diff
ORDER BY
  level_difference DESC
LIMIT 1
```

### 7. 각 트레이너가 가진 포켓몬 중에서 공격력(attack)이 100 이상인 포켓몬과 100 미만인 포켓몬의 수를 각각 계산해주세요. 트레이너의 이름과 두 조건에 해당하는 포켓몬의 수를 출력해주세요.
#### 풀이
```sql
SELECT 
    t.name,
    COUNT(CASE WHEN p.attack >= 100 THEN 1 END) AS attack_over_100,
    COUNT(CASE WHEN p.attack < 100 THEN 1 END) AS attack_below_100
FROM basic.trainer AS t
LEFT JOIN basic.trainer_pokemon AS tp
ON t.id = tp.trainer_id
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
GROUP BY 
    t.id, t.name
ORDER BY 
    t.name;
```
#### 해설
#### 정답

# 8. 마치며
