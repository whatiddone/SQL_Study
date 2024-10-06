> # 3-4. 오류를 디버깅하는 방법
- 대표적인 오류: Syntax Error(문법 오류)
  - 문법을 지키지 않아 발생
  - Error Message를  해석하여 해결 방법 찾아보기

#### 오류 예시들

```
Syntax error: SELECT list must not be empty at [3:1]

해석: SELECT 절에 컬럼이 비어있어 생기는 오류

-> SELECT 절에 컬럼을 추가하여 해결
```
```
Syntax error: Number of arguments does not match for aggregate function COUNT.

해석 집계 함수 COUNT에 여러 인자를 넣었기 때문에 발생

-> COUNT 함수에 하나의 인자만 넣어 해결

# COUNT 함수에는 한 번에 하나의 인자만 들어갈 수 있다.
```
```
Syntax error: SELECT list expression references column type1 which is neither grouped nor aggregated at [2:3]

# 해석: SELECT 목록 식은 다음에서 그룹화되거나 집계되지 않은 열을 참조합니다.

->  GROUP BY에 적절한 컬럼(type1)을 명시하여 해결
```
```
Syntax error: Expected end of input but got keyword SELECT at [8:2]

# 해석: 쿼리 입력이 끝날 것으로 예상되었지만 새 쿼리(SELECT)가 입력됨

-> 한 쿼리가 끝났을 때, 세미콜론(;)을 넣어 쿼리가 끝나는 지점을 명시하여 해결
```
```
Syntax error: Expected end of input but got keyword WHERE at [4:1]

# 해석: 쿼리 입력이 끝날 것으로 예상되었지만 WHERE이 입력됨

-> LIMIT을 삭제하거나 맨 아래로 옮겨 해결

# LIMIT은 쿼리의 가장 아래에 와야한다.
```
```
Syntax error: Expected ")" but got end of script at [8:9]

# 해석: )가 올 것으로 예상되었지만, 스크립트가 종료됨

-> 마지막에 )을 추가해줘서 서브쿼리를 마무리하여 해결
```
- 빨간 밑줄 앞뒤에서 오류가 존재할 가능성이 높다.
---

> # 4-1. INTRO
#### 이번 파트에서 다룰 내용
- 데이터 탐색: 변환
  - 자료형에 따른 여러 함수
  - 문자열
- 날짜 및 시간 데이터
- 조건문 함수
- BigQuery 공식 문서 확인하는 방법
---

> # 4-2. 데이터 타입과 데이터 변환(CAST, SAFE_CAST)
#### 데이터 타입
- 숫자(Int,Float)
- 문자(String)
- 시간, 날짜
- 부울(TRUE/FALSE)
- JSON, ARRAY 등 다양

데이터 타입이 중요한 이유: **보이는 것과 저장된 것의 차이가 존재**
- 엑셀에서 보면 빈 값 -> ""일 수도 있고, NULL일 수도 있음
- 1 이라고 작성된 경우 -> 숫자 1일 수도 있고, 문자 1일 수도 있음
- 2023-12-31 -> DATE 2023-12-31일 수도 있고, 문자 2023-12-31일 수도 있음
- TRUE/FALSE: 문자일 수도, 부울값일 수도 있음

#### 데이터 변환
- SELECT 문이나 WHERE 조건문에서 데이터를 변환시킬 수 있다.

자료 타입 변경 함수: CAST
```
SELECT
  CAST(1 AS STRING) # 숫자 1을 문자 1로 변경

SELECT
  CAST("DArt_B" AS INT64) # 문자를 숫자로 변경할 수 없으므로 오류발생

SELECT
  SAFE_CAST("DArt_B" AS INT64) # 변환 실패 시 NULL 반환
```

수학 함수
- 수학 연산(평균, 표준편차, 코사인 등)이 존재

나누기를 할 경우, <br> **x/y 대신 SAFE_DIVIDE 함수 사용하기** <br> SAFE_DIVIDE(x, y)<br> x, y 중 하나라도 0인 경우 그냥 나누면 zero error가 발생

---

> # 4-3. 문자열 함수(CONCAT, SPLIT, REPLACE, TRIM, UPPER)

- 문자열: "안녕하세요", "카일스쿨:

- 문자열 데이터로 할 수 있는 대표적인 연산
![7](/Basic%20Assignment/img/4th_week_img/7.png)

#### 문자열 붙이기: CONCAT
```
SELECT
  CONCAT("안녕", "하세요") AS result
# CONCAT 인자로 STRING이나 숫자를 넣을 떄는 데이터를 직접 넣어준 것 -> FROM 없이도 실행
# CONCAT("컬럼1","컬럼2",...)
```
![8](/Basic%20Assignment/img/4th_week_img/8.png)

#### 문자열 분리하기: SPLIT
```
SELECT
  SPLIT("가, 나, 다, 라", ", ") AS result
# SPLIT("문자열 원본", "나눌 기준이 되는 문자")
```
![9](/Basic%20Assignment/img/4th_week_img/9.png)

#### 특정 단어 수정하기: REPLACE
```
# 특정 단어 수정하기 => REPLACE
SELECT
  REPLACE("안녕하세요", "안녕", "실천") AS result
# REPLACE(문자열 원본, 찾을 단어, 바꿀 단어)
```
![10](/Basic%20Assignment/img/4th_week_img/10.png)

#### 문자열 자르기: TRIM
```
SELECT
  TRIM("안녕하세요", "하세요") AS result
# TRIM(문자열 원본, 자를 단어)
```
![11](/Basic%20Assignment/img/4th_week_img/11.png)

#### 영어 소문자를 대문자로 변경: UPPER
```
SELECT
  UPPER("abc") AS result
```
![12](/Basic%20Assignment/img/4th_week_img/12.png)

---

> # 4-4. 날짜 및 시간 데이터 이해하기(1)(타임존, UTC, Millisecond, TIMESTAMP/DATETIME)
#### 날짜 및 시간 데이터의 핵심
1. 날짜 및 시간 데이터 타입 파악: DATE, DATETIME, TIMESTAMP
2. 날짜 및 시간 데이터 관련 알면 좋은 내용: UTC, Millisecond
3. 날짜 및 시간 데이터 타입 변환하기
4. 시간 함수(두 시간의 차이, 특정 부분 추출하기)

#### 시간 데이터 다루기
- DATE, DATETIME, TIME, TIMESTAMP
  - DATE: 2002-03-10 (DATE 만 표시하는 데이터)
  - DATETIME: 2002-03-10 16:00:00 (DATE와 TIME까지 표시하는 데이터, Time Zone 정보 없음)
  - TIME: 23:59:59.00 (날짜와 무관하게 시간만 표시하는 데이터)
  - TIMESTAMP: 2023-12-31 14:00:00 UTC (UTC부터 경과한 시간을 나타내는 값, Time Zone 정보 있음)

- 타임존
  - UTC(협정 세계시, 한국시간: UTC+9)

- millisecond, microsecond
  - millisecond: 1000ms=1s. (정밀한 데이터를 위해 사용)
  - microsecond(μs): 1000μs=1ms

```sql
SELECT
  TIMESTAMP_MILLIS(1704172819711) AS milli_to_timestamp_value,
  TIMESTAMP_MICROS(1704172819711000) AS micro_to_timestamp_value,
  DATETIME(TIMESTAMP_MICROS(1704172819711000))AS datetime_value,
  DATETIME(TIMESTAMP_MICROS(1704172819711000), 'Asia/Seoul')AS datetime_value_asia;
# Datetime에서는 타임존을 넣었는지 체크하기
# Timestamp는 자동으로 UTC로 계산
```
![13](/Basic%20Assignment/img/4th_week_img/13.png)

#### TIMESTAMP와 DATETIME 비교
![14](/Basic%20Assignment/img/4th_week_img/14.png)
```
timestamp: 주로 UTC 기준으로 시간 데이터를 저장하고, 데이터베이스 서버의 시간대에 따라 값을 자동으로 변환해줘요. 따라서 시간대 차이가 있는 환경에서 사용할 때 유리하고, 특히 로그 데이터나 시스템 관련 데이터를 다룰 때 유용해요. 다만, 일반적으로 1970년 1월 1일 이후의 시간을 기록할 수 있는 범위가 있어요(2038년까지).

datetime: 특정 시간대에 상관없이 그 자체로 시간을 기록하고, 시간대에 의한 자동 변환이 없어요. 특정 시간대를 고정해놓고 데이터에 사용해야 하는 경우에 유리해요. 예를 들어, 과거 이벤트나 로컬 시간대 기반의 일정을 기록하는 경우 적합해요.

요약
timestamp는 시간대 변환이 필요하거나 서버의 시간이 변동될 가능성이 있는 경우 적합해요.
datetime은 특정 시간대와 무관하게 고정된 시간 정보를 저장하고 싶을 때 좋아요.
```