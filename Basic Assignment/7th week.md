># 6-1. Intro
- 가독성을 챙기기 위한 SQL 스타일 가이드
- 데이터 결과 검증
- 데이터 결과 검증 예시
![img](/Basic%20Assignment/img/7th_week_img/1.png)

># 6-2. 가독성을 챙기기 위한 SQL 스타일 가이드
1. 예약어(SELECT, FROM, WHERE, 각종 함수)는 대문자로 작성
  - SQL에서 문법적인 용도로 사용하고 있는 문자들은 대문자로 작성

2. 컬럼 이름은 snake_case로 작성(일관성이 중요)

3. 명시적 vs 암시적인 이름
  - Alias로 별칭을 지을 떄는 명시적인 이름을 적용
  - AS a, AS b 등 컬럼의 의미를 한번 더 생각하게 이름이 아닌, 명시적인 것을 사용
  - JOIN 할 때 테이블의 이름도 명시적으로 할 수 있다면 명시적으로 진행하기
  - AS를 생략해서 별칭을 설정할 수도 있는데, AS를 쓰는 것도 명시적인 표현

4. 기본적으로 왼쪽 정렬을 기준으로 작성
```sql
SELECT
 col
FROM table
WHERE 1=1
```

5. 예약어나 컬럼은 한 줄에 하나씩 권장
  - 컬럼은 바로 주석처리 할 수 있는 장점이 있기에 한 줄에 하나씩 작성
```sql
SELECT
  col1,
  col2,
  col3
FROM table
WHERE
```

6. 쉼표는 컬럼 바로 뒤에
  - 의견이 갈리는 부분(쉼표 앞 vs 뒤)
    - BigQuery는 마지막 쉼표를 무시해서 뒤에 작성해도 무방
```sql
SELECT
 col1,
 col2,
 col3,
FROM table
```
vs
```sql
SELECT
  co11
  ,col2
  ,col3
FROM table
```
># 6-3. 가독성을 챙기기 위한 WITH 문 & 파티션
- with 구문
  - CTE(Common Table Expression)라고 표현
  - SELECT 구문에 이름을 정해주는 것과 유사
  - 쿼리 내에서 반복적으로 사용할 수 있음
```sql
# WITH 구문 사용 방법
WITH name_a AS(
  SELECT
    col
  FROM Table
), name_b AS(
  SELECT
   col2
  FROM Table 2
)
![img](/Basic%20Assignment/img/7th_week_img/4.png)

-- 여기까지 WITH 구문으로 저장

SELECT
 col
FROM name_a
```
- PARTITION
  - 장점
   1. 쿼리 성능 향상
   - 전체 데이터를 스캔하는 것보다 파티션을 설정한 곳만 스캔하는 것이 더 빠름
   2. 데이터 관리 용이성
   - 특정 일자의 데이터를 모두 변경하거나 삭제해야 하면 파티션을 설정해서 삭제할 수 있음
   3. 비용
   - 파티션에 해당되는 데이터만 스캔해서 비용을 줄일 수 있음
![img](/Basic%20Assignment/img/7th_week_img/2.png)
![img](/Basic%20Assignment/img/7th_week_img/3.png)
- battle_datetime 별로 파티션이 나눠져있기 때문에, battle_datetime의 조건을 수정하면 조회하는 건수가 달라진다.

># 6-4. 데이터 결과 검증 정의
데이터 결과 검증: SQL 쿼리 후 얻은 결과가 예상과 일치하는지 확인하는 과정
- 중요한 부분
  - 문제를 잘 정의하고, 미리 작성해보기
  - 도메인 특수성 잘 파악하기
  - SQL 쿼리 템플릿이랑 맥락이 유사
![img](/Basic%20Assignment/img/7th_week_img/5.png)

데이터 결과를 검증할 때 자주 활용하는 SQL 쿼리
1. COUNT(*): 행 수를 학인, 의도한 데이터의 행 개수가 맞는가?
2. NOT NULL: 특정 컬럼에 NULL이 존재하는가? 필수 필드가 비어있지 않는가?
3. DISTINCT: 데이터의 고유값을 확인해 중복 여부 확인
4. IF문, CASE WHEN: 의도와 같다면 TRUE, 아니면 FALSE

데이터 결과를 검증하는 방법
1. 특정 user_id로 필터링을 걸어서 확인
- 1명의 데이터확인(예: WHERE user_id = 402) 
- 1명 데이터의 예상 결과와 쿼리 결과가 동일한지 확인
- 다른 user_id 3~4건 더 추가해서 확인(여러 케이스 존재 가능)
- 3~4개에서 동일한 결과가 나오면 user_id 조건을 삭제

2. 샘플 데이터 생성하기
- WITH문을 사용해 예시 데이터를 생성
- 결과를 예상하고 쿼리 작성
- 복잡한 데이터에서 하기 전에, 쿼리 자체가 올바른지 확인할 때 주로 사용

* UNION은 중복제거, UNION ALL은 중복제거 X

># 6-5. 데이터 결과 검증 예시
![img](/Basic%20Assignment/img/7th_week_img/6.png)
1. 전체 데이터 파악
2. 특정 user_id 선정
3. 승률 직접 COUNT : 결과 예상
4. 쿼리 작성
5. 실제와 비교
6. 맞다면 특정 유저 조건 제외

># 6-6. 정리
- 데이터 검증의 흐름
1. 문제 정의 확인
2. Input/Output 확인, 중간 과정도 생각하기
3. 쿼리 작성+가독성 챙기기
4. 예상 결과와 실제 쿼리 결과 비교
5. 오류가 없으면 끝내기, 오류가 있으면 다시 쿼리 작성

데이터 결과 검증시 자주 활용하는 SQL 쿼리
1. COUNT(*): 행 수를 확인
2. NOT NULL: 특정 컬럼에 NULL이 있는지 확인
3. DISTINCT: 데이터의 고유값을 확인해 중복 여부 확인
4. IF문, CASE WHEN: 의도와 같다면 TRUE, 아니면 FALSE

데이터 결과를 검증하는 방법
1. 전체 데이터 파악
2. 특정 user_id 선정
3. 승률 직접 COUNT: 결과 예상
4. 쿼리 작성
5. 실제와 비교
6. 맞다면 특정 유저 조건 제외