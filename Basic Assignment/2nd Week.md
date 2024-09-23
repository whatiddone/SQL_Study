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
![image](https://github.com/user-attachments/assets/eda64c8d-a4ab-43da-b6e3-7c756b059b1d)
*Syntax error: SELECT list must not be empty at [3:1]: SELECT 단계에서 컬럼이 지정되지 않았을 때 오류

