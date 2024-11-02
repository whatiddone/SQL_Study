> # 4-4. 날짜 및 시간 데이터 이해하기(2)(EXTRACT, DATETIME_TRUNC, PARSE_DATATIME, FORMAT_DATETIME)
#### DATETIME 함수 - CURRENT_DATETIME
- CURRENT_DATETIME([time_zone]): 현재 DATETIME 출력
```
SELECT
 current_date() AS current_date,
 current_date("Asia/Seoul") AS Seoul_date,
 current_datetime() AS current_datetime,
 current_datetime("Asia/Seoul") AS current_datetime_Seoul;
```
![1](/Basic%20Assignment/img/5th_week_img/1.png)

#### DATETIME 함수 - EXTRACT
- EXTRACT(part FROM datetime_expression): DATETIME에서 특정 부분(연/월/일 등)만 추출하고 싶은 경우
- part 부분에는 연/월/일뿐만 아니라, 주차/시간/분/초 등 다양한 시간 단위가 들어갈 수 있다.
```
SELECT
 EXTRACT(DATE FROM current_datetime("Asia/Seoul"))AS date,
 EXTRACT(YEAR FROM current_datetime("Asia/Seoul"))AS year,
 EXTRACT(MONTH FROM current_datetime("Asia/Seoul"))AS month,
 EXTRACT(DAY FROM current_datetime("Asia/Seoul"))AS day,
 EXTRACT(HOUR FROM current_datetime("Asia/Seoul"))AS hour,
 EXTRACT(MINUTE FROM current_datetime("Asia/Seoul"))AS minute,
 # 요일추출
 EXTRACT(DAYOFWEEK FROM current_datetime("Asia/Seoul"))AS dayofweek,
 current_datetime("Asia/Seoul") AS current_datetime_Seoul;
```
![2](/Basic%20Assignment/img/5th_week_img/2.png)
* DAYOFWEEK 함수는 일요일이 1, 월요일이 2,..., 토요일이 7이다. 쿼리 작성일이 토요일이므로, DAYOFWEEK 함수가 반환하는 값은 7.

#### DATETIME 함수 - DATETIME_TRUNC
- DATETIME_TRUNC(datetime_expression, date_time_part): 시간 자르기
- "2024-01-02 14:42:13"을 HOUR로 자르면 "2024-01-02 14:00:00
```
SELECT
 current_datetime("Asia/Seoul")AS original_time,
 datetime_trunc(current_datetime("Asia/Seoul"), day) AS day_trunc,
 datetime_trunc(current_datetime("Asia/Seoul"), year) AS year_trunc,
 datetime_trunc(current_datetime("Asia/Seoul"), month) AS month_trunc,
 datetime_trunc(current_datetime("Asia/Seoul"), hour) AS hour_trunc;
``` 
![3](/Basic%20Assignment/img/5th_week_img/3.png)

#### DATETIME 함수 - PARSE_DATETIME
- PARSE_DATETIME('문자열의 형태','DATETIME 문자열'): 문자열로 저장된 DATETIME을 **DATETIME 타입으로** 바꾸고 싶은 경우
```
SELECT
  PARSE_DATETIME('%Y-%m-%d %H:%M:%S', '2024-11-02 17:24:24') AS parse_datetime;
```
![4](/Basic%20Assignment/img/5th_week_img/4.png)

#### DATETIME 함수 - FORMAT_DATETIME
- FORMAT_DATETIME("DATETIME 타입의 형태", datetime "문자열"): DATETIME 타입 데이터를 **문자열 데이터로** 바꾸고 싶은 경우
```
SELECT
  FORMAT_DATETIME("%c", DATETIME '2024-11-02 17:24:24') AS formatted;
```
![5](/Basic%20Assignment/img/5th_week_img/5.png)

** DATETIME 타입의 형태
![6](/Basic%20Assignment/img/5th_week_img/6.png)

```
문자열 => DATETIME: PARSE_DATETIME
DATETIME => 문자열: FORMAT_DATETIME
```

#### DATETIME 함수 - LAST_DAY
- LAST_DAY(DATETIME, 시간 단위): 특정 시간 단위(연,월,주)의 마지막 날을 알고 싶은 경우 
```
SELECT
  LAST_DAY(DATETIME '2024-11-02 17:40:18') AS last_day,# 기본값은 month
  LAST_DAY(DATETIME '2024-11-02 17:40:18', MONTH) AS last_day_month, #11월 2일이 있는 달의 마지막 날
  LAST_DAY(DATETIME '2024-11-02 17:40:18', WEEK) AS last_day_week, #11월 2일이 있는 주의 마지막 날
  LAST_DAY(DATETIME '2024-11-02 17:40:18', WEEK(SUNDAY)) AS last_day_week_sun,# WEEK의 기본값은 일요일
  LAST_DAY(DATETIME '2024-11-02 17:40:18', WEEK(MONDAY)) AS last_day_week_mon;#월요일을 한 주의 시작으로 잡았을 때, 11월 2일이 있는 주의 마지막 날
```
![7](/Basic%20Assignment/img/5th_week_img/7.png)

#### DATETIME 함수 - DATETIME_DIFF
- DATETIME_DIFF(첫 DATETIME, 두번째 DATETIME, 궁금한 차이)
```
SELECT
    DATETIME_DIFF(first_datetime, second_datetime, DAY) AS day_diff1, # 일 수 계산
    DATETIME_DIFF(second_datetime, first_datetime, DAY) AS day_diff2, # 계산할 때는 'first_datetime'-'second_datetime'
    DATETIME_DIFF(first_datetime, second_datetime, MONTH) AS month_diff, # 월 수 계산
    DATETIME_DIFF(first_datetime, second_datetime, WEEK) AS week_diff, # 주 수 계산
FROM(
    SELECT
      DATETIME "2024-04-02 10:20:15" AS first_datetime,
      DATETIME "2024-02-11 15:30:05" AS second_datetime,
    );
```
![8](/Basic%20Assignment/img/5th_week_img/8.png)

#### 정리
- 날짜 및 시간 데이터 타입
  - DATE
  - DATETIME:DATE+TIME, 타임존정보X
  - TIMESTAMP:특정시점에 도장찍은값타임존정보O 
  - UTC:국제적인표준시간.한국은UTC+9 
  - Millisecond: 1/1000초
  - Microsecond: 1/1000ms
- 시간 데이터 타입 변환하기
  - TIMESTAMP_MILLIS: 밀리초를 TIMESTAMP로
  - TIMESTAMP_MICROS: 마이크로초를 TIMESTAMP로
  - DATETIME(TIMESTAMP, TIMEZONE): TIMESTAMP를 DATETIME으로
  - 문자열 => DATETIME: PARSE_DATETIME
  - DATETIME => 문자열: FORMAT_DATETIME
  - 현재 DATETIME: CURRENT_DATETIME([time_zone])
  - DATETIME 특정 부분 추출: EXTRACT(파트 FROM datetime)
  - DATETIME 특정 부분 자르기: DATETIME_TRUNC(datetime_expression, 파트)
  - DATETIME 차이 구하기: (첫 DATETIME, 두번째 DATETIME, 파트)

> # 4-5. 시간 데이터 연습 문제
### 1. 트레이너가 포켓몬을 포획한 날짜(catch_date)를 기준으로, 2023년 1월에 포획한 포켓몬의 수를 계산해주세요.
#### 풀이
* 포켓몬의 수 집계
* 포획 날짜 기준
```sql
SELECT 
  sum(Total_Pokemon)as all_Pokemon_in_JAN2023
FROM(
  SELECT
    catch_date,
    count(id) AS Total_Pokemon
  FROM basic.trainer_pokemon
  GROUP BY catch_date)
WHERE DATETIME_TRUNC(catch_date,month) = '2023-01-01'; 
```
- 86개
![9](/Basic%20Assignment/img/5th_week_img/9.png)

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 : 포켓몬의 수
# 쿼리 계산 방법 : COUNT
# 데이터의 기간 : 2023년 1월! => catch_datetime을 사용해야 함 => EXTRACT
# 사용할 테이블: trainer_pokemon
# Join KEY : X
# 데이터 특징 : 직접 봐야함!
  -- catch_date : DATE 타입
  -- catch_datetime : UTC. TIMESTAMP 타입 => 컬럼의 이름은 datetime인데 TIMESTAMP 타입으로 저장되어 있다!
  -- catch_date의 기준이 UTC인지 KR인지 확인 필요
  -- catch_date, catch_datetime 컬럼을 비교 => DATE(DATETIME(catch_datetime, "Asia/Seoul"))
```
```
-- #0. 데이터 검증을 위한 쿼리
SELECT
  id,
  catch_date,
  DATE(DATETIME(catch_datetime, "Asia/Seoul")) As catch_datetime_kr_date
FROM basic.trainer_pokemon
```
![10](/Basic%20Assignment/img/5th_week_img/10.png)
```
catch_datetime 값과 catch_datetime을 서울 시간대로 변환한 값이 서로 다르다.
```
```
SELECT
  COUNT(*)
FROM (
  SELECT
    id,
    catch_date,
    DATE(DATETIME(catch_datetime, "Asia/Seoul")) As catch_datetime_kr_date
    FROM basic.trainer_pokemon
    ) 
WHERE
  catch_date != catch_datetime_kr_date > 141
  catch_date = catch_datetime_kr_date > 238
# != : 같지 않다. 같지 않은 경우는 141건, 같은 경우는 238건
```
**컬럼의 설명을 꼭 확인하고 SQL을 작성**
#### 정답
```sql
SELECT
  COUNT(DISTINCT id) AS cnt
FROM basic.trainer_pokemon
WHERE
  EXTRACT(YEAR FROM DATETIME(catch_datetime, "Asia/Seoul")) = 2023 # catch_datetime은 TIMESTAMP로 저장되어 있으므로, DATETIME으로 변경해야 함
  AND EXTRACT(MONTH FROM DATETIME(catch_datetime, "Asia/Seoul")) = 1
```
### 2. 배틀이 일어난 시간(battle_datetime)을 기준으로, 오전 6시에서 오후 6시 사이에 일어난 배틀의 수를 계산해주세요.
#### 풀이
* 배틀의 수 집계
* 배틀 시간 기준
```sql
SELECT
 count(DISTINCT id) AS cnt
FROM basic.battle
WHERE 
  EXTRACT(HOUR FROM battle_datetime) >= 6
  AND EXTRACT(HOUR FROM battle_datetime) <= 18
```
- 44개
![11](/Basic%20Assignment/img/5th_week_img/11.png)

#### 설명
```
# 쿼리를 작성하는 목표, 확인할 지표 : 오전 6시 ~ 오후 6시 배틀의 수
# 쿼리 계산 방법 : COUNT
# 데이터의 기간 : 일자는 상관없고, 오전 6시 ~ 오후 6시 => battle_datetime => EXTRACT!
# 사용할 테이블: battle
# Join KEY : X
# 데이터 특징
-- battle_datetime과 DATETIME(battle_timestamp, "Asia/Seoul")이 같은지 확인!
SELECT
  COUNTIF(battle_datetime = DATETIME(battle_timestamp, "Asia/Seoul")) AS battle_datetime_same_battle_timestamp_kr, # battle_datetime = DATETIME(battle_timestamp, "Asia/Seoul")이 같은 행의 수
  COUNTIF(battle_datetime != DATETIME(battle_timestamp, "Asia/Seoul")) AS battle_datetime_not_same_battle_timestamp_kr # battle_datetime = DATETIME(battle_timestamp, "Asia/Seoul")이 다른 행의 수
FROM basic.battle
```
#### 정답
```
SELECT
 count(DISTINCT id) AS cnt
FROM basic.battle
WHERE 
  EXTRACT(HOUR FROM battle_datetime) BETWEEN 6 and 18
  # HOUR FROM battel_datetime이 '숫자'이기 때문에 BETWEEN 사용 가능
  # BETWEEN a and b -> a와 b 사이에 있는 것을 반환
```
![11](/Basic%20Assignment/img/5th_week_img/11.png)

```
# 시간대별로 몇 건이 있는가?
SELECT
  hour,
  COUNT(DISTINCT id) AS battle_cnt
FROM (
  SELECT
   *,
   EXTRACT(HOUR FROM battle_datetime) AS hour
  FROM basic.battle
  )
GROUP BY
  hour
ORDER BY
  hour
```
![12](/Basic%20Assignment/img/5th_week_img/12.png)

### 3. 각 트레이너별로 그들이 포켓몬을 포획한 첫 날(catch_date)을 찾고, 그 날짜를 'DD/MM/YYYY' 형식으로 출력해주세요. (2024-01-01=>01/01/2024)
#### 풀이
* DD/MM/YYYY 형식으로 출력
* 트레이너별 포켓몬을 포획한 첫 날
* 서브쿼리로 첫 날을 찾고나서 날짜 출력 형태 바꾸기
```
```
#### 설명
#### 정답
### 4. 배틀이 일어난 날짜(battle_date)를 기준으로, 요일별로 배틀이 얼마나 자주 일어났는지 계산해주세요.
#### 풀이
#### 설명
#### 정답
### 5. 트레이너가 포켓몬을 처음으로 포획한 날짜와 마지막으로 포획한 날짜의 간격이 큰 순으로 정렬하는 쿼리를 작성해주세요.
#### 풀이
#### 설명
#### 정답

> # 4-6. 조건문 함수

> # 4-7. 조건문 연습 문제

