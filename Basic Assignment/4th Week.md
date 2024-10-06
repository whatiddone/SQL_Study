> # 3-4. 오류를 디버깅하는 방법
- 대표적인 오류: Syntax Error(문법 오류)
  - 문법을 지키지 않아 발생
  - Error Message를  해석하여 해결 방법 찾아보기

- 오류 예시들

![1](/Basic%20Assignment/img/4th_week_img/1.png)
```
Syntax error: SELECT list must not be empty at [3:1]

해석: SELECT 절에 컬럼이 비어있어 생기는 오류

-> SELECT 절에 컬럼을 추가하여 해결
```
![2](/Basic%20Assignment/img/4th_week_img/2.png)
```
Syntax error: Number of arguments does not match for aggregate function COUNT.

해석 집계 함수 COUNT에 여러 인자를 넣었기 때문에 발생

-> COUNT 함수에 하나의 인자만 넣어 해결

# COUNT 함수에는 한 번에 하나의 인자만 들어갈 수 있다.
```
![3](/Basic%20Assignment/img/4th_week_img/3.png)
```
Syntax error: SELECT list expression references column type1 which is neither grouped nor aggregated at [2:3]

# 해석: SELECT 목록 식은 다음에서 그룹화되거나 집계되지 않은 열을 참조합니다.

->  GROUP BY에 적절한 컬럼(type1)을 명시하여 해결
```
![4](/Basic%20Assignment/img/4th_week_img/4.png)
```
Syntax error: Expected end of input but got keyword SELECT at [8:2]

# 해석: 쿼리 입력이 끝날 것으로 예상되었지만 새 쿼리(SELECT)가 입력됨

-> 한 쿼리가 끝났을 때, 세미콜론(;)을 넣어 쿼리가 끝나는 지점을 명시하여 해결
```
![5](/Basic%20Assignment/img/4th_week_img/5.png)
```
Syntax error: Expected end of input but got keyword WHERE at [4:1]

# 해석: 쿼리 입력이 끝날 것으로 예상되었지만 WHERE이 입력됨

-> LIMIT을 삭제하거나 맨 아래로 옮겨 해결

# LIMIT은 쿼리의 가장 아래에 와야한다.
```
![6](/Basic%20Assignment/img/4th_week_img/6.png)
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
```
![8](/Basic%20Assignment/img/4th_week_img/8.png)

#### 문자열 분리하기: SPLIT
```
```
#### 특정 단어 수정하기: REPLACE
```
```
#### 문자열 자르기: TRIM
```
```
#### 영어 소문자를 대문자로 변경: UPPER
```
```

---

> # 4-4. 날짜 및 시간 데이터 이해하기(1)(타임존, UTC, Millisecond, TIMESTAMP/DATETIME)