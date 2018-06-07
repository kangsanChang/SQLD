### 문제풀며 틀린 것들 개념 정리

#### 데이터 조작어
- 비절차적 데이터 조작어 : 사용자가 무엇을 원하는가 (What)
- 절차적 데이터 조작어 : 사용자가 무슨 데이터를 어떻게 접근해야 하는가 (How) (PL/SQL 같은 것)

#### Constraint
- 세 가지 방식
  - Col 정의 시 옆에 바로 적저우기
  - 외부에서 걸기
    ```SQL
    ALTER TABLE (TBNAME) ADD CONSTRAINT PRIMARY KEY (CONSTRAINT_NAME) ON (ATTR_NAME)
    ```
  - 내부에서 걸기
    ```SQL
    CREATE ... ( ...
      ,CONSTRAINT (CONSTRAINT_NAME) PRIMARY KEY (ATTR_NAME)
    )
    ```
#### RENAME
  ```SQL
  RENAME A TO B;
  ```
#### REPLACE(A,B,C)
- A :target , B: Condition , C: 대체할놈(생략시 그냥 없어짐))

#### IMPLICIT 변환
- '001' 과 같은 문자를 숫자 1 과 비교하기 위해선 '001' 로 되어있는 col의 값들이 전부 '00x'와 같은 문자 형식으로 저장되어있어야 가능함.

#### SQL SERVER
- column 여러개를 한 명령에 수정 불가능. alter 를 각각 col에 하나씩 해줘야함

#### TB의 COL 삭제
  ```SQL 
  ALTER TABLE (TBNAME) DROP COLUMN (COLNAME);
  ```

#### Referential Actions
  - DELETE(or Modify) Action
    - CASCADE / SET NULL / SET DEFAULT / RESTRICT(Child 에 PK없을때만 가능)
    - 아무것도 하지 않으면 Referential Integrity 해치는 모든 삭제/수정 x
  - Insert Action
    - AUTOMATIC(Master에 PK 없는경우 Master PK 생성 후 Child 입력)/ SET NULL / SET DEFAULT / DEPENDENT (Master TB에 PK 있을때만 가능)
    - 아무것도 하지 않으면 Referential Integrity 해치는 모든 입력 x

#### 시간 비교시 유의점
  - 1월 한 달 동안의 내역을 보고 싶은 경우 (18년 1월 내의 DATETIME : JAN_DATE 라 하면)
    - '201801' = TO_CHAR(JAN_DATE, 'YYYYMM') : 가능함
    - TO_DATE('201801', 'YYYYMM') = JAN_DATE : 한 경우 빼고 다 불가능함
      - TO_DATE('201801', 'YYYYMM') 은 2018년 01월 1일 0시 0분 0초 를 의미하기 때문

#### 내장함수
- 단일행 함수와 다중행 함수로 나눌 수 있음
- 다중행 함수 : 집계함수 / 그룹함수 / 윈도우 함수 로 구분됨
- 다중행 함수도 단일행 함수와 동일하게 단일 값만을 반환

#### CASE EXPRESSION
- SEARCHED_CASE_EXPRESSION
  ```sql
  CASE WHEN LOC = 'NEW YORK' THEN 'EAST'
  ```
- SIMPLE_CASE_EXPRESSION
  ```sql
  LOC WHEN 'NEW YORK' THEN 'EAST'
  ```

#### GROUP BY 와 HAVING 절 특징
- GROUP BY 를 통해 소그룹별 기준을 정한 후 SELECT 절에 집계함수를 사용.
- 집계 함수의 통계 정보는 NULL 값을 가진 행을 제외하고 수행
- GROUP BY 에서는 SELECT 와는 달리 ALIAS 를 사용 할 수 없다.
- 집계 함수는 WHERE 절에는 올 수 없다. (GROUP BY 보다 WHERE이 먼저 수행 되기 떄문!!)
- WHERE 절은 전체 데이터를 GROUP 으로 나누기 전에 행들을 미리 제거시킨다
- HAVING 절은 GROUP BY 절의 기준 항목이나 소그룹의 집계 함수를 이용한 조건을 표시할 수 있다.
- GROUP BY 절에 의한 소그룹별로 만들어진 집계 데이터 중 HAVING 절에서 제한을 두어 조건을 만족하는 내용만 출력한다.
- HAVING 절은 일반적으로 GROUP BY 뒤편에 위치한다.

#### Correlated SubQuery
- WHERE 절 내부에 옴. ( FROM 절에 사용 불가능)
- 사용 시 식별자.(ATTR) 로 찝어줘야 함.

#### 중첩된 그룹함수
- <b>중첩된 그룹함수는 최종 결과값이 1건</b>이 될 수 밖에 없다!!! 그러므로 GROUP BY 에 기술되었더라도 사용 SELECT 에 적힐 수 없다.
- 고로 아래 SQL 은 잘못된 것이다.
```SQL
/* 틀림 */
SELECT 메뉴ID, 사용유형코드, AVG(COUNT(*)) AS AVGCNT FROM 사용이력 GROUP BY 메뉴ID, 사용유형코드;
```

#### ORDER BY 유의점
- GROUP BY 가 먼저 수행되기 때문에 Table 에서 사용하는 Column이 바뀜. 고로 없는 Column(Group by에 기술되지 않은) 으로 정렬 불가능.

#### SELECT 실행 순서
- FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY

#### SQL SERVER
- TOP() 사용 시 TIES 붙여주면, 중복된 건수를 다 뽑아주고 그만큼 더 뽑아줌. 
- TOP(3)에서 2등이 2명일 경우 최종 뽑히는 애들은 1,2,2,3 등 4명

#### DBMS optimizer 는 FROM 절에 나열된 TB 를 임의로 2개씩 묶어서 Join 수행

