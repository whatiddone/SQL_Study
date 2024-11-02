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
  catch_date as 
  count(id) as Total_Pokemon
FROM trainer_pokemon
WHERE
 
```
#### 설명
#### 정답
### 2. 배틀이 일어난 시간(battle_datetime)을 기준으로, 오전 6시에서 오후 6시 사이에 일어난 배틀의 수를 계산해주세요.
#### 풀이
#### 설명
#### 정답
### 3. 각 트레이너별로 그들이 포켓몬을 포획한 첫 날(catch_date)을 찾고, 그 날짜를 'DD/MM/YYYY' 형식으로 출력해주세요. (2024-01-01=>01/01/2024)
#### 풀이
#### 설명
#### 정답
### 4. 배틀이 일어난 날짜(battle_date)를 기준으로, 요일별로 배틀이 얼마나 자주 일어났는지 계산해주세요.
#### 풀이
#### 설명
#### 정답
### 5. 트레이너가 포켓몬을 처음으로 포획한 날짜와 마지막으로 포획한 날짜의 간격이 큰 수능로 정렬하는 쿼리를 작성해주세요.
#### 풀이
#### 설명
#### 정답

> # 4-6. 조건문 함수

> # 4-7. 조건문 연습 문제

