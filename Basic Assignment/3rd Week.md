> # 2-6. 연습문제 1~3번
---
### 1. 포켓몬 중에 type2가 없는 포켓몬의 수를 작성하는 쿼리를 작성해주세요
* '수'를 작성 -> count 함수 사용하기 -> count는 집계 함수 -> GROUP BY 필요!
* type 2가 없는 -> 조건 -> WHERE
```
SELECT
  type2,
  count(id) as CNT
FROM basic.pokemon
WHERE
 type2 IS NULL
GROUP BY type2;
```

![1번문제](/Basic%20Assignment/3rd_week_img/Q1.png)

#### 설명

- 조건: type2가 없는
  - type2가 어떻게 생겼지?(문자열) type2의 정의는 무엇이지?
- NULL은 뭘까?
  - 아무것도 없는 값. 값이 존재하지 않을 때 NULL
  - NULL은 0이랑 다르고, ""과도 다름 -> 값이 없는 상태
  - type2 = NULL은 안되나? -> X, NULL은 다른 값과 직접 비교할 수 없음. 'NULL = NULL'은 거짓이 아니라 알 수 없음
  - 핵심 : NULL은 IS 연산자를 사용한다!
  - NULL 값만 빼고 로드하려면 '컬럼이름' IS NOT NULL
- 어떤 테이블: pokemon
- 어떤 컬럼: 따로 없음, 포켓몬의 수만 남기면 됨.
- 어떻게 집계: 포켓몬의 수 -> COUNT
