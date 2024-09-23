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

 - ex) 포켓몬 타입이 붙은 포켓몬을 찾는 SQL 쿼리
 SELECT
  * 
 FROM basic.pokemon
 WHERE
  type1="Fire"
    - *: 모든 컬럼을 출력하겠다.(데이터 확인용으로 쓰는데, 미리보기로 대체 가능)
    - SELECT
       *EXCEPT(제외할 컬럼)
       이런 형태도 가능
