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
> # 4-1. INTRO
> # 4-2. 데이터 타입과 데이터 변환(CAST, SAFE_CAST)
> # 4-3. 문자열 함수(CONCAT, SPLIT, REPLACE, TRIM, UPPER)
> # 4-4. 날짜 및 시간 데이터 이해하기(1)(타임존, UTC, Millisecond, TIMESTAMP/DATETIME)