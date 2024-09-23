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
- 특정 Tabled에 있는 데이터를 각각 추출 후, 연결하기(Join)

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



> # 2-3. 데이터 탐색(SELECT, FROM, WHERE)
---
