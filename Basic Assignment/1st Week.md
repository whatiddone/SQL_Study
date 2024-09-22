> # Preview
---
### 시작하기 전에 알고 있으면 좋은 개념
함수: 프로그램 소스 코드에서 일정한 동작을 수행하는 코드들 (엑셀함수랑 똑같은 거!)

- SQL을 처음 접한다면?

일단 실행해보고 ⇒ 쿼리문을 완성하는 것에 초점을 맞추기


> # 1-1. BigQuery 기초 지식
---
### 데이터 저장 형태
- Database: 데이터의 저장소
- Table: 데이터가 저장된 공간
- Database>Table>Row(행,가로)&Column(열,세로)

### 데이터가 저장되는 장소
- DBMS(Database Management System)에 저장 (MySQL, Oracle, PostgreSQL 등)
- MySQL, Oracle, PostgreSQL과 같은 데이터베이스의 특징: OLTP
  - OLTP(Online Transaction Processing)란?
      - 거래를 하기 위해 사용되는 데이터베이스
      - 데이터가 무결하다: 보류, 중간 상태가 없다.
      - 데이터의 추가(INSERT), 데이터의 변경(UPDATE)이 많이 발생.

### SQL(Structurer Query Language)
- SQL: 데이터베이스에서 ***데이터를 가지고 올 때 사용**하는 언어

### 데이블에 저장된 데이터의 형태
| 구조 | 모양 | 의미 |
|:---:|:---:|:---:|
| 행(Row) | 가로 | 하나의 Row가 하나의 고유한 데이터 |
| 열(Column) | 세로 | 각 데이터의 특정 속성 값 |

*위 표에서 '열'은 '구조, 모양.의미', '행'은 '행.열'
