### 06-4 날짜 데이터를 다루는 날짜 함수
|연산|설명|
|----|----|
|날짜 데이터 + 숫자 | 날짜 데이터보다 숫자만큼 일수 이후의 날짜 | 
|날짜 데이터 - 숫자 | 날짜 데이터보다 숫자만큼 일수 이전의 날짜 |
|날짜 데이터 - 날짜 데이터|두 날짜 데이터 간의 일수 차이|
|날짜 데이터 + 날짜 데이터 | 연산 불가, 지원하지 않음 |

##### SYSDATE 함수 : 오라클 데이터베이스 서버가 놓인 OS의 현재 날짜와 시간을 보여줌
```sql
SELECT SYSDATE AS NOW,
       SYSDATE - 1 AS YESTERDAY,
       SYSDATE + 1 AS TOMORROW
  FROM DUAL;
```
#### 몇 개월 이후 날짜를 구하는 ADD_MONTHS 함수
```sql
-- 3개월 뒤의 날짜 구하기
SELECT SYSDATE,
       ADD_MONTHS(SYSDATE, 3)
  FROM DUAL;
  
-- 입사 10주년이 되는 사원들 데이터 추력하기
SELECT EMPNO, ENAME, HIREDATE,
       ADD_MONTHS(HIREDATE, 120) AS WORK10YEAR
  FROM EMP;
  
-- 입사 32년 미만인 사원 데이터 출력하기
SELECT EMPNO,
       ENAME, HIREDATE, SYSDATE
  FROM EMP
 WHERE ADD_MONTHS(HIREDATE, 384) > SYSDATE;

-- SYSDATE와 ADD_MONTHS 함수를 사용하여 현재 날짜와 6개월 후 날자가 출력되게!
SELECT SYSDATE,
       ADD_MONTHS(SYSDATE, 6)
  FROM DUAL;
```
#### 두 날짜 간의 개월 수 차이를 구하는 MONTHS_BETWEEN 함수
```sql
-- 비교 날짜의 입력 위치에 따라 음수 또는 양수가 나올 수 있다.
-- 개월 수 차이는 소수점 단위까지 결과가 나오므로 TRUNC함수를 조합하면 개월 수 차이를 정수로 출력할 수도 있다.
SELECT EMPNO, ENAME, HIREDATE, SYSDATE,
       MONTHS_BETWEEN(HIREDATE, SYSDATE) AS MONTHS1,
       MONTHS_BETWEEN(SYSDATE, HIREDATE) AS MONTHS2,
       TRUNC(MONTHS_BETWEEN(SYSDATE, HIREDATE)) AS MONTH3
  FROM EMP;
```
#### 돌아오는 요일, 달의 마지막 날짜를 구하는 NEXT_DAY, LAST_DAY 함수
```sql
-- NEXT_DAY : 특정 날짜를 기준으로 돌아오는 요일의 날짜를 출력해주는 함수
-- LAST_DAY : 특정 날짜가 속한 달의 마지막 날짜를 출력해주는 함수

SELECT SYSDATE,
       NEXT_DAY(SYSDATE, '월요일'),
       LAST_DAY(SYSDATE)
  FROM DUAL;
```
#### 날짜의 반올림, 버림을 하는 ROUND, TRUNC 함수
> 날짜 기준 포맷의 종류를 다 외울 필요는 없다. 다만, ROUND 함수를 이용한 반올림과 TRUNC 함수를 이용한 버림이 날짜 데이터에도 적용 가능하다는 것만 기억해두자

### 06-5 자료형을 변환하는 형변환 함수
```sql
-- EMP에 속한 열들의 자료형 확인해보기
DESC EMP;
-- 숫자와 문자열(숫자)을 더하여 출력하기
SELECT EMPNO, ENAME, EMPNO + '500'
  FROM EMP
 WHERE ENAME = 'SCOTT';
```
> 작은따옴표로 묶인 500은 분면 문자뎅리터이지만 숫자 자료형인 사원 번호 열 값과 수치 연산이 가능했던 것은      
> '자동형변환'이라고도 불리는 암시적 형 변환이 발생했기 때문이다.     
#### 명시적 형변환    

|종류|설명|
|-----|----|
|TO_CHAR  | 숫자 또는 날짜 데이터를 문자 데이터로 변환|
|TO_NUMBER| 문자 데이터를 숫자 데이터로 변환 |
|TO_DATE| 문자 데이터를 날짜 데이터로 변환|        

```숫자 데이터 <--> 문자 데이터 <---> 날짜 ```
#### 날짜, 숫자 데이터를 문자 데이터로 변환하는 TO_CHAR 함수
```sql
-- SYSDATE 날짜 형식 지정하여 출력하기(2022/07/20 18:04:02)
SELECT TO_CHAR(SYSDATE, 'YYYY/MM/DD HH24:MI:SS') AS 현재날짜시간
  FROM DUAL;
  
-- 특정 언어에 맞춰서 날짜 출력하기
SELECT SYSDATE,
       TO_CHAR(SYSDATE, 'MON', 'NLS_DATE_LANGUAGE = ENGLISH' ) AS MON_ENG,
       TO_CHAR(SYSDATE, 'MON', 'NLS_DATE_LANGUAGE = JAPANESE' ) AS MON_JPN,
       TO_CHAR(SYSDATE, 'MON', 'NLS_DATE_LANGUAGE = KOREAN' ) AS MON_KOR
  FROM DUAL; 
```
#### 문자 데이터를 숫자 데이터로 변환하는 TO_NUMBER 함수
```sql
-- 쉼표 때문에 암시적 형변환이 일어나지 않음
SELECT '1,300' - '1,500'
  FROM DUAL;

-- TO_NUMBER 함수로 연산하여 출력하기
SELECT TO_NUMBER('1,300', '999,999') - TO_NUMBER('1,500', '999,999')
  FROM DUAL;
```
#### 문자 데이터를 날짜 데이터로 변환하는 TO_DATE 함수
```sql
SELECT TO_DATE('2022'07-20', 'YYYY-MM-DD') AS TODATE 1,
       TO_DATE('20220720', 'YYYY-MM-DD') AS TODATE 2
  FROM DUAL;

-- 1981년 6월 1일 이후에 입사한 사원 정보 출력하기
SELECT *
  FROM EMP
 WHERE HIREDATE >TO_DATE('1981/06/01', 'YYYY/MM/DD');

-- 1980년 10월 15일 이후에 입사한 사원 출력하기
SELECT *
  FROM EMP
 WHERE HIREDATE > TO_DATE('1980/10/15', 'YYYY-MM-DD');
```
> 두 자리로 연도를 표현할 때 사용하는 YY, RR는 사용상 주의를 기울여야 한다.

### 06-6 NULL 처리 함수
특정 열의 데이터가 NULL일 경우에 연산수행을 위해 데이터를 다른 값으로 대체해주어야 할 때가 종종 발생한다.     
이 때, NVL함수와 NVL2함수를 유용하게 사용한다.    
#### NVL 함수의 기본 사용법
```sql
-- NVL 함수를 사용하여 출력하기
-- COMM 열의 NULL 값을 0으로 대체해서 연산을 가능케함 
SELECT EMPNO, ENAME, SAL, COMM, SAL+COMM,
       NVL(COMM, 0),
       SAL+NVL(COMM, 0)
  FROM EMP;
```
#### NVL2 함수의 기본 사용법
NVL2함수는 NVL함수 + NULL일 경우 반환할 데이터 지정(금액과 같이 민감한 데이터의 노출을 피할 수 있음)    
```sql
-- NVL2(데이터 또는 열, NULL이 아닐 경우 반환할 데이터, NULL일 경우 반환할 데이터)
SELECT EMPNO, ENAME, COMM,
       NVL2(COMM, 'O', 'X'),
       NVL2(COMM, SAL*12+COMM, SAL*12) AS ANNSAL
  FROM EMP;
```
> 특정 열 값이나 데이터 값에 따라 어떤 데이터를 반환할지 정할 때는 DECODE함수 또는 CASE문을 사용한다.

#### DECODE 함수
```
DECODE([검사 대상이 될 열 또는 데이터, 연산이나 함수의 결과],
      [조건1], [데이터가 조건1과 일치할 때 반환할 결과],
      [조건2], [데이터가 조건2와 일치할 때 반환할 결과],
      ...
      [조건n], [데이터가 조건n과 일치할 때 반환할 결과],
      [위 조건1~조건n과 일치한 경우가 없을 때 반환할 결과])
```
```sql
-- EMP 테이블에서 직책이 MANAGER인 사람은 급여의 10%를 인상한 급여, 
-- SALESMAN인 사람은 급여의 5%, ANALYST인 사람은 그대로, 나머지는 3%만큼 인상된 급여를 보고싶다!
SELECT EMPNO, ENAME, JOB, SAL,
       DECODE(JOB,
              'MANAGER', SAL*1.1,
              'SALESMAN', SAL*1.05,
              'ANALYST', SAL,
              SAL*1.03 AS UPSAL
  FROM EMP;
```
> 만약, DECODE함수의 맨 마지막 데이터, 즉 조건에 해당하는 값이 없을 때 반환값을 지정하지 않으면 NULL이 반환된다.

#### CASE문
DECODE함수와 마찬가지로 특정 조건에 다라 반환할 데이터를 설저할 때 사용.     
CASE문의 경우, 각 조건에 사용하는 데이터가 서로 상관없어도 된다. 또, 기준 데이터 값이 같은(=) 데이터 외에 다양한 조건을 사용할 수 있다.   
```
CASE [검사 대상이 될 열 또는 데이터, 연산이나 함수의 결과(선택)]
   WHEN [조건1] THEN [조건1의 결과값이 true일 때, 반환할 결과]
   WHEN [조건2] THEN [조건2의 결과값이 true일 때, 반환할 결과]
   WHEN [조건3] THEN [조건3의 결과값이 true일 때, 반환할 결과]
   ...
   WHEN [조건n] THEN [조건n의 결과값이 true일 때, 반환할 결과]   
   ELSE [위 조건1~조건n과 일치하는 경우가 없을 때 반환할 결과]
END   
```
```sql
-- EMP 테이블에서 직책이 MANAGER인 사람은 급여의 10%를 인상한 급여, 
-- SALESMAN인 사람은 급여의 5%, ANALYST인 사람은 그대로, 나머지는 3%만큼 인상된 급여를 보고싶다!
SELECT EMPNO, ENAME, JOB, SAL,
       CASE JOB
            WHEN 'MANAGER' THEN SAL*1.1
            WHEN 'SALESMAN' THEN SAL*1.05
            WHEN 'ANALYST' THEN SAL
            ELSE SAL*1.03 
       END AS UPSAL
       FROM EMP;

-- 기준 데이터 없이 조건식만으로 CASE문 사용하기
SELECT EMPNO, ENAME, COMM,
    CASE
     WHEN COMM IS NULL THEN '해당사항 없음'
     WHEN COMM = 0 THEN '수당없음'
     WHEN COMM > 0 THEN '수당 : ' || COMM
    END AS COMM_TEXT
  FROM EMP;
```
> **기억하자** : DECODE함수와 CASE문은 모두 조건별로 동일한 자료형의 데이터를 반환해야 한다!!!!
#### <연습문제>
```sql
--Q1 다시풀기
SELECT EMPNO,
  FROM EMP
  WHERE LENGTH(ENAME) >= 5
    AND LENGTH(ENAME) < 6;

--Q2
SELECT EMPNO, ENAME, SAL, 
       TRUNC(SAL/21.5, 2) AS DAY_PAY,
       ROUND(SAL/21.5/8, 1) AS TIME PAY
  FROM EMP;


--Q3 다시풀기
SELECT EMPNO, ENAME, HIREDATE, 
       NEXT_DAY(ADD_MONTHS(HIREDATE, 3), '월요일') AS R_JOB, NVL(COMM, N/A) AS COMM
FROM EMP;

--Q4
SELECT EMPNO, ENAME, MGR, 
    CASE MGR
     WHEN MGR IS NULL THEN '0000'
     WHEN SUBSTR(MGR, 1, 2) = '75' THEN '5555'
     WHEN SUBSTR(MGR, 1, 2) = '76' THEN '6666'
     WHEN SUBSTR(MGR, 1, 2) = '77' THEN '7777'
     WHEN SUBSTR(MGR, 1, 2) = '78' THEN '8888'
     ELSE TO_CHAR(MGR)
    END AS CHG_MGR
FROM EMP;

```

