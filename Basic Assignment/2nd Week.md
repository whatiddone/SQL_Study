> # 2-3. 데이터 탐색(SELECT, FROM, WHERE)
---
### SQL 쿼리 구조
SQL 쿼리문은 아래와 같은 문법으로 작성
- 우선 SELECT, FROM, WHERE
#### SELECT: 테이블의 **어떤 컬럼**을 선택(출력)할 것인가?
#### FROM: **어떤 테이블**에서 데이터를 확인할 것인가?
#### WHERE: **만약 원하는 조건**이 있다면 어떤 조건인가?
```
SELECT
 Col1, AS new_name (As 를 사용하면 컬럼 이름을 변경할 수 있다.)
 Col2,
 Col3
FROM Dataset.Table
WHERE
 Col1=1
```
* SELECT에서 As 를 사용하면 컬럼 이름을 변경할 수 있다.
* WHERE 뒤에는 조건문이 오는 느낌이다. (예: Name="꼬부기")

ex) 포켓몬 타입이 붙은 포켓몬을 찾는 SQL 쿼리
```
SELECT
 * 
FROM basic.pokemon
WHERE
 type1="Fire"
```
- *: 모든 컬럼을 출력하겠다.(데이터 확인용으로 쓰는데, 미리보기로 대체 가능)
```   
- SELECT
 *EXCEPT(제외할 컬럼)
```
이런 형태도 가능

### 데이터가 여러 장소에 저장되어 있는 경우
- 특정 Table에 있는 데이터를 각각 추출 후, 연결하기(Join)

### BigQuery에서 쿼리 실행하기
```
SELECT
 id as pokemon_id, # AS는 별칭을 지어줄 때 사용한다.
 -- id as"pokemon_id" # 컬럼 이름에 따옴표는 넣어서 문자로 넣어주면 오류가 발생
 -- id as pokemon_id # 컬럼 여러개 쓸 때는 ','로 구분해주기
 kor_name, 
 type1,
 total

FROM `my-sql-1st-week.basic.pokemon`

WHERE
 type1="Fire"; # ";"는 쿼리문이 하나 끝났다는 것을 알려준다.

SELECT
 id
FROM basic.pokemon;

# my-sql-1st-week: project id
# basic: dataset
# pokemon: table
# <project id>,<dataset>,<table>
# 단일 프로젝트라면 project id는 꼭 명시할 필요는 없다.
# 프로젝트를 여러개 사용한다면 명시해야 한다. => 쿼리를 실행할 때 어떤 프로젝트인지 확인하는 과정이 존재
# project id를 제외하고 사용해도 괜찮긴하다.
# 데이터를 활용하고 싶은 목적이 있어야, 어떤 컬럼을 선택할지 알 수 있게 된다.

# 가독성 있는 쿼리: 특히나 협업할 때 중요
```
*Syntax error: SELECT list must not be empty at [3:1]: SELECT 단계에서 컬럼이 지정되지 않았을 때 오류

- 주석 여러개 다는 법: 드래그한 다음 "Ctrl+/"
- 쿼리문 여러 개 있을 때 선택해서 실행하는 법: 드래그 한 다음에 "Ctrl+Enter"

### SQL 문법 핵심 정리
|  | 설명 |
|:---:|:---|
| FROM | 데이터를 확인할 Table 명시 <br> 이름이 너무 길다면 AS "별칭"으로 처리가능 <br> FROM Table1 AS T1|
| WHERE | FROM에 명시된 Table에 저장된 데이터를 필터링(조건설정) <br> Table에 있는 컬럼을 조건 설정|
| SELECT | Table에 저자되어 있는 컬럼 선택 <br> 여러 컬럼 명시 가능 <br> col1 AS "별칭"으로 컬럼의 이름도 별칭 지정 가능 |

- 쿼리 엔진의 실행 순서: FROM->WHERE->SELECT: 이 순서대로 데이터 구조를 생각하기
- **쿼리문 실행할 때는 반대 순서! (SELECT-FROM-WHERE)**

### 수행 인증
![image](https://github.com/user-attachments/assets/8adf09ee-39cd-4cdc-a18a-3c4af87f7cba)



> # 2-4. SELECT 연습 문제
---
### 연습문제 1. Trainer 테이블에 있는 모든 데이터를 보여주는 SQL 쿼리를 작성해주세요.
1) Trainer 테이블에 어떤 데이터가 있는지 확인해보자
2) Trainer 테이블을 어디에 명시해야 할까? ==>FROM
3) 필터링 조건이 있을까? ==> 모든 데이터 ==> 필터링을 할 필요가 없겠다.(WHERE X)
4) 모든 데이터 ==> 모든 데이터 = 모든 컬럼일 수도 있겠다(추측) 쿼리 작성 => 애매하면 모든 데이터의 정의가 무엇인가?
```
SELECT
 *
FROM basic.trainer;
```
![image](https://github.com/user-attachments/assets/bbd52f47-5ef3-4765-9d26-24dc112495fc)

### 연습문제 2. Trainer 테이블에 있는 트레이너의 name을 출력하는 쿼리를 작성해주세요
```
SELECT
 name
FROM basic.trainer;
```
![image](https://github.com/user-attachments/assets/9ea815cd-c272-4b2f-a35d-0f86eba36166)

### 연습문제 3. Trainer 테이블에 있는 name, age를 출력하는 쿼리를 작성해주세요.
```
SELECT
 name,
 age
FROM basic.trainer;
```
![image](https://github.com/user-attachments/assets/aeeb1355-59d1-48b8-a5f6-e604a03b169b)

### 연습문제 4. Trainer의 테이블에서 id가 3인 트레이너의 name, age, hometown을 출력하는 쿼리를 작성해주세요.
*조건 등장: id=3
```
SELECT
 name,
 age,
 hometown
FROM basic.trainer
WHERE id=3;
```
![image](https://github.com/user-attachments/assets/3c05e805-9e3c-474a-bd6f-573001331575)

### 연습문제 5. Pokemon 테이블에서 "피카츄"의 공격력과 체력을 확인할 수 있는 쿼리를 작성해주세요.
*조건 등장: "피카츄"
```
SELECT
 hp,
 attack,
 kor_name
 FROM basic.pokemon
 WHERE kor_name="피카츄";
```
 ![image](https://github.com/user-attachments/assets/5367cafd-7a6a-4a5f-96f9-28d1aedeafe5)
- Select에서 'kor_name'이 빠지면 쿼리 결과만으로는 피카츄의 공격력과 체력인지 아닌지 보기 어려워서 Select에 피카츄 추가

> # 2-5. 집계(GROUP BY+HAVING+SUM/COUNT)
### 집계와 그룹화
- 집계하다 = 모아서 계산하다 = 그룹화해서 계산하다.
- 계산
  - 더하기, 빼기
  - 최대값, 최소값
  - 평균
  - 갯수 세기

 ### 집계: GROUP BY 
 - GROUP BY: 같은 값끼리 모아서 그룹화한다.
   - 특정 컬럼을 기준으로 모으면서 다른 컬럼에서는 집계 가능 (합, 평균, MAX,MIN 등)
     - "타입"을 기준으로 그룹화해서 "평균 공격력" 집계하기
     - "타입"을 기준으로 그룹화해서 "타입 별 포켓몬 수" 집계하기
     - "평균 공격력"을 내림차순으로 정렬하면, "평균 공격력"이 제일 높은 타입을 알 수 있고 <br> "타입 별 포켓몬 수"가 가장 낮은 타입을 알고 싶으면, "타입 별 포켓몬 수"를 오름차순으로 정렬하면 된다.
* 정렬: ORDER BY

### 그룹화 한 값에 조건 설정하기
- 타입 당 포켓몬 수가 10마리 이상인 데이터만 추출하고 싶은 경우: 집계를 한 이후이기 때문에 **HAVING** 사용

### 함수를 사용하는 경우
```
SELECT
 집계할_컬럼1,
 집계 함수(COUNT,MAX,MIN 등)
FROM Table
GROUP BY
 집계할_컬럼1
```
 **집계할 컬럼을 SELECT에 명시하고 그 컬럼을 꼭 GROUP BY에 작성**<br>
 **->COUNT, SUM, AVG, MAX, MIN 같은 **집계 함수(Aggregate Functions)**를 사용할 때는 GROUP BY가 필요**

 ### 집계 함수 종류
| Name | Summary |
|:---:|:---|
| AVG | Gets the average of non-NULL values. |
| COUNT | 	Gets the number of rows in the input, or the number of rows with an expression evaluated to any value other than NULL. |
| COUNTIF | Gets the count of TRUE values for an expression. |
| MAX | 	Gets the maximum non-NULL value. |
| MIN | 	Gets the minimum non-NULL value. |
| SUM | 	Gets the sum of non-NULL values. |

-전체 리스트
![image](https://github.com/user-attachments/assets/c2e26786-c4be-429d-8ea7-75


### DISTINCT: 고유값을 알고 싶은 경우(중복을 제거)
- 예) 1,2,3,3,4 => DISTINCT하면 => 1,2,3,4
```
SELECT
 집계할 컬럼,
 COUNT(DISTINCT count할 컬럼)
FROM table
GROUP BY
    집계할 컬럼
```

### GROUP BY 연습 문제 1. pokemon 테이블에 있는 포켓몬 수를 구하는 쿼리를 작성해주세요.
```
# 그룹화할 때 사용할 컬럼: X
# 집계할 때 사용할 계산: 수를 구한다 => COUNT

SELECT
  COUNT(id) AS cnt
FROM basic.pokemon
```

### GROUP BY 연습 문제 2. 포켓몬의 수가 세대별로 얼마나 있는지 알 수 있는 쿼리를 작성해주세요
```
# 그룹화할 때 사용할 컬럼: generation
# 집계할 때 사용할 계산: 얼마나 있는지 => 수를 구한다 => COUNT

SELECT
 generation,
 COUNT(id) AS cnt
FROM basic.pokemon
GROUP BY
 generation;
```

### 그룹화(집계) 활용 포인트
데이터 분석하다가 그룹화하는 경우
- 일자별 집계(원본 데이터는 특정 시간에 어떤 유저가 한 행동이 기록, 일자별로 집계)
- 연령대별 집계(특정 연령대에서 더 많이 구매했는가?)
- 특정 타입별 집계(특정 제품 타입을 많이 구매했는가?)
- 앱 화면별 집계(어떤 화면에 유저가 많이 접근했는가?)


### 조건을 설정하고 싶은 경우: WHERE
WHERE
- 테이블에 바로 조건을 설정하고 싶은 경우 사용   
- Raw Data인 테이블 데이터에서 조건 설정
```
SELECT
 컬럼1, 컬럼2,
 COUNT(컬럼1) AS col1_count
FROM <table>
WHERE
 컬럼1 >= 3
```
### 조건을 설정하는 경우: HAVING
HAVING
- **GROUP BY한 후** 조건을 설정하고 싶은 경우 사용
- 새롭게 만든 컬럼(이미 한 번 계산한 결과)에 대해 조건을 걸 수 있음
```
SELECT
 컬럼1, 컬럼2
 COUNT(컬럼1) AS col1_count
FROM <table>
GROUP BY 컬럼1, 컬럼2
HAVING
 col1_count > 3
```
*쿼리의 FROM 절에 다른 쿼리(서브쿼리)가 들어갈 수 있다.

### 조건을 설정하고 싶은 경우: WHERE이랑 HAVING의 차이는?
- WHERE
  - Table에 바로 조건을 사용하고 싶은 경우 사용
- HAVING
  - GROUP BY한 후 조건을 설정하고 싶은 경우 사용

- 같이 사용하는 경우
```
SELECT
 컬럼1, 컬럼2
 COUNT(컬럼1) AS col1_count
FROM <table>
WHERE
 컬럼1 >= 3
GROUP BY 컬럼1, 컬럼2
HAVING
 col1_count > 3
```

### 서브쿼리
- SELECT문 안에 존재하는 SELECT 쿼리
- FROM 절에 또다른 SELECT문을 넣을 수 있음
- 괄호로 묶어서 사용
- 서브쿼리를 작성하고, 서브쿼리 바깥에서 WHERE 조건 설정하는 것 = 서브쿼리에서 HAVING으로 하는 것
 ![image](https://github.com/user-attachments/assets/c17fb74b-bcaf-41fc-95e7-2b11a6647de8)


### 정렬하기: ORDER BY
```
SELECT
 col
FROM
ORDER BY 
  컬럼 순서;
```
- 순서: DESC(내림차순), OSC(오름차순-보통 Default임)
- ORDER BY는 쿼리의 마지막에 작

### 출력 개수 제한하기: LIMIT
- 쿼리문의 결과 Row 수를 제한하고 싶은 경우 사용.

**쿼리문의 제일 마지막에 작성**
```
SELECT
    col
FROM Table
LIMIT 10;
```
ORDER BY와 함께 맨 아래에 위치

### GROUP BY 연습 문제 3. 포켓몬의 수를 타입별로 집계하고, 포켓몬의 수가 10 이상인 타입만 남기는 쿼리를 작성해주세요. 포켓몬의 수가 많은 순으로 정렬해주세요.
```
# 타입별로 집계 => SELECT type1, GROUP BY type1
# 포켓몬의 수를 세는 것 => COUNT(id)
# GROUP BY 이후 조건: 포컷몬의 수가 10 이상 => HAVING
포켓몬의 수가 많은 순으로 정렬: => 내림차순정렬 => ORDER BY 포켓몬 수 DESC

SELECT
 type1,
 count(id) as cnt
FROM basic.pokemon
GROUP BY type1 
HAVING cnt>=10
order by cnt DESC;
```

### 요약, 집계, 그룹화 정리
- 집계: GROUP BY + 집계 함수(AVG, MAX 등)
- 고유값: DISTINCT
- 조건: WHERE/HAVING
- 정렬: ORDER BY
- 출력 개수 제한: LIMIT
