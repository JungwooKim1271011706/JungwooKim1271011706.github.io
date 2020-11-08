---
title: Oracle DB - 01 (SELECT)
category:
- Oracle DB
tag:
- Oracle DB
---

오라클은 DB로 SQL 문법을 사용해서 SELECT, INSERT, UPDATE, DELETE 를 할 수 있다. 이 중 가장 많이 사용하는 구문은 SELECT이다.

SELECT 구문은 아래와 같이 사용한다.

	SELECT [DISTINCT] {*, COLUMN[ALIAS], , , ] FROM 테이블명;
	
--SELECT : 질의하는 구문 키워드  
--[] : 브라켓 : 사용해도 되고 사용하지 않아도 된다.  
--DISTINCT : 질의된 결과 고루에서 중복을 제거하는 키워드  
--SELECT DISTINCT : DISTINCT 키워드 위치가 SELECT 키워드 다음이라는 의미  
--[DISTINCT] : DISTINCT 키워드를 사용해도 되고 사용하지 않아도 된다는 의미  
--{} : 브레이스 : 배열 표시 : 순차적으로 나열하라는 의미  
--{*, COLUMN[ALIAS], .....} : 첫번째 자리에 * 를 사용하고 다음에 컬럼명을 순차적으로 사용하는데 구분자는 "," (CSV) 이다.  
--COLUMN[ALIAS] : 컬럼의 별칭(ALIAS)를 사용할 수도 있고, 사용하지 않아도 된다는 의미  
--COLUMN ALIAS : AS 키워드를 사용하지 않아도 상관 없다.  
--FROM : 테이블을 가리키는 키워드  
--테이블명 : 질의할 테이블

FROM 뒤에 종결을 시키지 않고 WHERE 절을 사용하면 SELECT문의 조건을 부여할 수 있다.

	--EMP 테이블에서 ENAME이 "SMITH"인 사람의 HIREDATE를 조회하시오.
	SELECT
		A.HIREDATE AS HIREDATE
	FROM EMP A
	WHERE A.ENAME = "SMITH";

	--필드가 2개 이상인 경우에는 WHERE IN 을 사용할 수 있다.
	WHERE A.ENAME IN ("SMITH", "ALLEN") -- SMITH와 ALLEN의 데이터를 보여주세요.
	
	-- WHERE 절에서는 등호연산도 가능하다.
	WHERE ENAME > 'SMITH' --등호연산, 문자도 가능하다.

SELECT 절의 컬럼명에 MAX(), MIN()과 같은 함수를 집어넣으면 최대값, 최소값을 찾아낼 수 있다.

	--EMP 테이블에서 HIREDATE의 최대값을 조회하시오.
	SELECT MAX(A.HIREDATE) AS HIREDATE FROM EMP A;
	--EMP 테이블에서 HIREDATE의 최소값을 조회하시오. 
	SELECT MIN(A.HIREDATE) AS HIREDATE FROM EMP A; 
	
FROM 절의 뒤에 ORDER BY INDER/LABEL ASC/DESC 를 추가하면 조회하는 데이터를 오름차순/내림차순 정렬할 수 있다. 데이터 조회의 기본값은 오름차순 정렬이기 때문에 ASC/DESC를 기재하지 않으면 기본값으로 ASC로 조회한다.

	--EMP 테이블에서 COLUMN INDEX 가 1이거나 COLUMN LABEL이 EMPNO인 데이터를 내림차순으로 조회하시오.
	SELECT * FROM EMP ORDER BY 1/EMPNO DESC;

	-- EMP 테이블에서 COLUMN INDEX 가 1이거나 COLUMN LABEL이 ENPNO 인 데이터를 오름차순으로 조회하시오.
	SELECT * FROM EMP ORDER BY 1/EMPNO ASC; 
	
DUAL 테이블은 오라믈에서 제공하는 dummy table이다. 한 행과 한 컬럼을 담고 있으며 산술 연산을 지원한다.

	SELECT * FROM DUAL;
	SELECT 1 + 2 FROM DUAL;
	-- DUMP () 는 해당 필드의 데이터타입, 길이, 아스키코드를 반환한다.
	SELECT DUMP(' SMITH ') FROM DUAL;

	--TRIM 은 공백 제거 , 앞단 공백과 끝단  공백을 지운다.
	--LTRIM, RTRIM은 각각 좌측, 우측 공백을 지운다.
	SELECT DUMP(TRIM(' SMITH ')) FROM DUAL;
	SELECT DUMP(LTRIM(' SMITH ')) FROM DUAL;
	SELECT DUMP(RTRIM(' SMITH ')) FROM DUAL;
	
	
오라클에서 날짜 데이터를 출력할 경우에는 TO_CHAR(), TO_DATE() 함수를 사용한다.
	
	SELECT TO_CHAR(TO_DATE(20201103), 'YYYY-MM-DD')) FROM DUAL;
	-- TO_CHAR 로 포맷을 지정할 수 있다. 문자열로 반환한다.
	
구간검색은 연산자를 사용해야 정확하게 연산이 가능하다.

	SELECT
		A.*
	FROM EMP A
	WHERE SAL >= 1000 AND SAL <= 3000;

	-- 검색시에는 와일드카드를 사용하면 검색의 효율성을 높일 수 있다.
	-- LIKE 절은 대상문자열이 포함되어 있는 데이터를 가져온다.
	SELECT * FROM EMP WHERE ENAME LIKE 'K%';

COUNT() 함수는 해당하는 테이블의 로우 수를 리턴한다.

TAB는 해당하는 계정의 테이블 이름을 담고 있는 테이블이다. 따라서 이 쿼리문은 해당하는 계정의 테이블 개수를 구할 때  사용한다. TAB은 TNAME, TABTYPE, CLUSTERID를 리턴한다. TNAME에 해당 계정의 테이블 이름이 출력된다.

	SELECT COUNT(*) FROM TAB; 

DESC 를 사용하면 해당 테이블의 구조를 파악할 수 있다.

	DESC TAB; --이름/널/데이터 타입이 조회된다.

SELECT *  FROM USER_TABLES; -- SELECT* FROM TABS 와 동일하다 해당 계정의 테이블을 조회하는데 TAB 보다 자세한 정보가 출력된다.

	ROWNUM
	쿼리에 추출된 각 행에 부여되는 일련번호
	쿼리 추출값을 1부터 시작하여 순차값으로 할당한다.

	-- 해당하는 테이블의 로우값을 조회해서 가상의 일련번호를 1부터 출력한다.
	SELECT ROWNUM FROM EMP 
	WHERE ROWNUM <= 5; -- WHERE 조건절을 추가해서 ROWNUM을 제한할 수 있다.

ROWID
테이블 내 행의 위치를 지정하는 논리값
DB 전체에 중복되지 않는 유일한 값을 출력한다.

	SELECT ROWID FROM EMP; -- EMP 테이블의 ROW의 고유한 ID값을 출력한다.

ID값은 아래와 같이 출력되는데, 각 영역마다 가리키는 값이 있다.

	/*
	AAAR3s//AAE//AAAACX//AAB
	AAAR3sAAEAAAACXAAC
	AAAR3s : 데이터 오브젝트 번호
	AAE : 데이터 파일 번호
	AAAACX : 데이터 블럭 번호
	AAA : 블럭내의 행번호
	*/

SYSDATE 키워드는 현재 날짜를 리턴한다.

	-- 현재 날짜를 리턴한다.
	SELECT SYSDATE FROM DUAL; 
	-- 현재 날짜를 리턴한다. +09:00 으로 GMT까지.
	SELECT SYSTIMESTAMP FROM DUAL;  

TO_CHAR를 사용하면 FORMAT을 변경할 수 있다.

	SELECT TO_CHAR(SYSDATE, 'YYYYMMDD') FROM DUAL;

	--FORAMT에 'HH24:MI:SS'  옵션을 주면 24시간 표기법으로 보여준다.
	SELECT TO_CHAR(SYSDATE, 'HH24:MI:SS') FROM DUAL;

	--FORAMTDP 'HH:MI:SS' 옵션을 주면 12시간 표기법으로 보여준다.
	SELECT TO_CHAR(SYSDATE, 'HH:MI:SS') FROM DUAL;

	-- 시계열 데이터의 연산은 +- 로 일자 계산도 가능하다.
	SELECT SYSDATE -1 FROM DUAL; 

현재 날짜에서 대상날짜의 차이를 구하는 경우에도 연산으로 쉽게 구할 수 있다.

	-- 19921206에서 현재 날짜까지 시간차를 일자로 계산하고
	SELECT TRUNC(SYSDATE - TO_DATE('19921206', 'YYYY/MM/DD')) FROM DUAL;
	--19921206에서 현재 날짜까지 시간차를 월로 계산하기
	SELECT TRUNC(MONTHS_BETWEEN(SYSDATE, '19921206')) FROM DUAL;
	--19921206에서 현재 날짜까지 시간차를 연도로 계산하기
	SELECT TRUNC(MONTHS_BETWEEN(SYSDATE, '19921206')/12,0) FROM DUAL;

또한, 컬럼 데이터 타입이 문자, 숫자, 날짜여도 조회된 결과들은 전부 문자열로 반환한다.

SELECT 절의 DISTINCT 키워드는 중복을 제거한다.

	--EMP 테이블에서 중복을 제거한 DEPTNO를 출력하시오
	SELECT DISTINCT DEPTNO FROM EMP;

DISTINCT 키워드 외에 GROUP BY 키워드를 통해서도 중복된 데이터를 제거할 수 있다.

	SELECT DEPTNO FROM EMP
	GROUP BY DEPTNO;

GROUP BY 함수에서는 HAVING 으로 그룹에 대한 조건을 부여할 수 있다.

	-- EMP 테이블에서 DEPTNO로 그룹을 형성한 뒤에 DEPTNO의 필드값이 30인 데이터를 출력한다.
	SELECT DEPTNO FROM EMP GROUP BY DEPTNO HAVING DEPTNO = 30;

오라클에서는 NULL 값이 저장되는 것을 허용한다.
NULL 값은 어떤 값이 존재하기는 하지만, 어떤 값인지 알아낼 수 없다는 것을 의미한다.
회원가입시 필수사항이 아닌 선택사항을 입력하지 않을 경우에 오라클에서는 NULL 값으로 저장한다.
하지만 다른 DB에 따라 다른 값이 들어갈 수도 있다.
WHERE 절에 IS NULL / IS NOT NULL 로 NULL 값 혹은 NULL 값이 아닌 데이터를 출력할 수도 있다.

	EMP 테이블에서 COMM 컬럼이 NULL 값인 것들 데이터를 조회하시오.
	SELECT * FROM EMP WHERE COMM IS NULL; 

NULL 값이 있는 경우에는 연산에 포함이 되지 않는다. 이러한 경우에는 NVL(NULL, 값)으로 NULL 값을 변경시켜줘야 한다. NVL(NULL, 값)은 조회하고자 하는 데이터가 NULL인 경우에는 두번째 매개변수 값으로 변경하라는 의미이다.

SELECT NVL(NULL, 0) FROM DUAL;

오라클 DB에서 정렬하는 경우, NULL 값이 포함된 컬럼이면 NULL 값이 숫자보다 크다고 오라클은 판단한다. 따라서 오름차순인 경우에는 기준 컬럼을 오름차순 정렬한 후에 NULL 값이 후행한다.
반면 내림차순인 경우에는 NULL 값이 선행하고, 기준 컬럼을 내림차순으로 정렬한다.

오라클에서 데이터를 출력할때 사용자가 컬럼을 추가해서 출력할 수도 있다. 이 경우에는 컬럼에 저장된 건 아니고, 출력에만 반영된다.

	-- 출력값이 A 전체컬럼이며, 9, 10 컬럼이 추가되어 테이블을 출력한다.
	SELECT A.* , 9, 10 FROM EMP A;

조건이 복잡해지면 서브 쿼리를 사용해야 하는 경우도 있다.

	-- EMP 테이블에서 입사일이 제일 늦은 사원의 이름을 조회하시오
	SELECT
		A.ENAME -- 이름 조회
	FROM EMP A -- EMP 테이블
	WHERE A.HIREDATE = (SELECT MAX(A.HIREDATE) FROM EMP A);
	--조건절이 입사일 중 제일 늦은 입사일을 찾아야 하기 때문에 A.HIREDATE에 쿼리문을 하나 더 붙여야 한다.

COUNT(), SUM(), ROUNT(), MAX(), MIN() 은 그룹 함수이다. 그룹 함수는 결과를 단일행(!!중요!!)으로 리턴한다.

ROLLUP 은 GROUP BY 처럼 특정 컬럼을 기준으로 그룹을 설정한다.
하단부에는 데이터를 합산한 결과값을 산출해서 보여준다.
특정 컬럼을 기준으로 오룸차순 정렬한다.

	--EMP 테이블에서 DEPTNO로 그룹을 형성한 후에 DEPTNO, SUM(SAL)을  출력하고 하단부에 합산값을 추출한다.
	SELECT DEPTNO, SUM(SAL) FROM EMP GROUP BY ROLLUP(DEPTNO);

	--ROLLUP 의 매개변수를 1개 이상으로 설정해도 가능하다.
	SELECT DEPTNO, JOB, SUM(SAL) FROM EMP GROUP BY ROLLUP((DEPTNO, JOB));

RANK () OVER() 는 순위를 부여하는 함수이다.

	--SAL 내림차순으로 RANK를 PK 컬럼에 부여한다. 공동순위가 잇으면 순위가 밀린다.
	SELECT EMPNO, ENAME, SAL,
			RANK() OVER(ORDER BY SAL DESC) AS PK
	FROM EMP;

	--SAL 내림차순으로 RANK를 PK에 부여한다. // 공동 순위가 있어도 순위가 안밀린다.
	SELECT EMPNO, ENAME, SAL,
			DENSE_RANK() OVER(ORDER BY SAL DESC) AS PK
	FROM EMP;

	-- OVER() 함수에 PARTITION BY 를 옵션으로 넣으면 DEPTNO로 분할해서 RANK를 출력할 수 있다.
	--부서별 순위를 알 수 있다.
	SELECT EMPNO, ENAME, SAL,
			RANK() OVER(PARTITION BY DEPTNO ORDER BY SAL DESC) AS PK
	FROM EMP;

	-- SAL을  내림차순으로 순서를 구하는데, 공동순위가 있어도 순위가 밀리지 않는다.
	--단 공동 SAL인 경우에 순위를 어떻게 정하는는 아직 확인하지 못했다..
	SELECT ROWNUM, EMPNO, ENAME, SAL,
			ROW_NUMBER() OVER(ORDER BY SAL DESC) AS PK
	FROM EMP;

UPPER(), LOWER() 함수는 대상문자열을 대문자 혹은 소문자로 바꾸는 기능을 한다. 테이블 컬럼의 필드들의 문자열 양식에 따라서 유용하게 사용할 수 있다.

SUBSTR() 첫번쨰 스펠링부터 1로 인덱스를 부여한다.
음수값을 줄 경우에는 뒤쪽부터 -1로 인덱스를 부여하고, 인덱스부터의 길이를 가지고 특정문자열을 뽑아낼 수 있다.

	SELECT
		 SUBSTR('DataBase', 1, 3)  >> Dat
		,SUBSTR('DataBase', 3) >> taBase
		,SUBSTR('DataBase', -4) >> Base
		,SUBSTR('DataBase', -4, 3) >> Bas
	FROM DUAL;

LPAD() 는 자릿수만큼 특정 문자열로 채워주는 함수이다.

	SELECT
		LPAD('DATABASE', 20, '$')
	FROM DUAL;
	--$$$$$$$$$$$$DATABASE
	--20길이만큼 데이터를 채운다. LPAD는 왼쪽 공간을 해당 문자열로 채운다.

RPAD도 사용법은 동일하다. 오른쪽에 남아있는 길이만큼 데이터를 채운다.

ROUND() 함수는 반올림 함수이다. 매개변수로 기재된 자릿수까지만 표시하며, 그 이하의 자릿수는 반올림한다. 자릿수가 없으면 기본값으로 정수단위만 표시한다.
매개변수가 음수이면 해당 정수범위로 반올림한다.

	SELECT
			 ROUND(12.135, 2) 
			,ROUND(12.135, 0)  
			,ROUND(12.135)
			,ROUND(12.135, -1)
	FROM DUAL;
	
DECODE(대상 컬럼, 기준 필드값, 데이터)
 지양하는 코드이다.  IF 문과 흡사하다. 대상 컬럼의 필드값이 기준 필드값과 동일하면 데이터를 집어넣어서 결과를 도출한다. 
 
	A.DEPTNO에서 필드값이 10인 필드를 ACCOUNTING 으로 채운다.
	SELECT A.DEPTNO, DECODE(A.DEPTNO, 10, 'ACCOUNTING') DNAME FROM DEPT A;
	SELECT 
		DEPTNO,
		DECODE(DEPTNO, 10, 'ACCOUNTING',
						20, 'RESEARCH',
						30, 'SALES') DNAME
						--40이 필드값에 있으나 값을 주지 않아서 DNAME은 NULL이 표시된다.
	FROM DEPT A;

위 코드를 대체하기 위해서 CASE  WHEN 조건 THEN 출력값 END 를 사용한다.

	--WHEN에 TABLE ALIAS 정보가 있어서 CASE에 기재하지 않아도 TABLE을 찾는다.
	SELECT      
		 SUM(CASE WHEN A.DEPTNO = '10' THEN MAX(A.SAL) END)
		,SUM(CASE WHEN A.DEPTNO = '20' THEN MAX(A.SAL) END)
		,SUM(CASE WHEN A.DEPTNO = '30' THEN MAX(A.SAL) END)
	FROM EMP A
	GROUP BY A.DEPTNO;
	
	SELECT
		SUM (CASE A.DEPTNO WHEN 10 THEN MAX(A.SAL) END)
	,SUM (CASE A.DEPTNO WHEN 20 THEN MAX(A.SAL) END)
	,SUM (CASE A.DEPTNO WHEN 30 THEN MAX(A.SAL) END)
		--이렇게 CASE 에 ALIAS와 COLUMN을 기재해도 위 코드와 동일한 기능을 수행한다.


부서별 급여 총계를 구하는 경우에는 아래 코드를 작성한다.

	SELECT
	--부서번호가 10인 사원들만 골라서 SUM 한다.
		DEPTNO, NVL(SUM(DECODE(DEPTNO, 10, SAL)), 0) DEPT10
	--부서번호가 20인 사원들만 골라서 SUM 한다.
		, NVL(SUM(DECODE(DEPTNO, 20, SAL)), 0) DEPT20
	--부서번호가 30인 사원들만 골라서 SUM 한다.
		, NVL(SUM(DECODE(DEPTNO, 30, SAL)), 0) DEPT30
	--부서번호가 40인 사원들만 골라서 SUM 한다. 
		, NVL(SUM(DECODE(DEPTNO, 40, SAL)), 0) DEPT40
	FROM EMP A
	GROUP BY DEPTNO;
	
	부서별 급여 합계를 하나의 열로 출력하는 경우 >> 유용한 쿼리문이다.
	
	SELECT
		 --부서별 사원들만 골라서 SUM을 한 코드에서는
		 --DECODE 한 부서 말고는 전부다 0이다.
		 --MAX함수를 사용해서 부서별 MAX 값만 추출하면
		 --부서별 급여 합계를 1 ROW로 출력할 수 있다.
     MAX(NVL(SUM(DECODE(A.DEPTNO, 10, SAL)), 0)) DEPT10
    ,MAX(NVL(SUM(DECODE(A.DEPTNO, 20, SAL)), 0)) DEPT20
    ,MAX(NVL(SUM(DECODE(A.DEPTNO, 30, SAL)), 0)) DEPT30
    ,MAX(NVL(SUM(DECODE(A.DEPTNO, 40, SAL)), 0)) DEPT40
	FROM EMP A
	GROUP BY DEPTNO;
	
	--부서별 급여 합계를 행으로 

	SELECT B.DEPTNO, B.DNAME, NVL(SUM(A.SAL), 0) SAL
	FROM EMP A, DEPT B
	WHERE A.DEPTNO(+) = B.DEPTNO
	-- 40이 DEPTNO에는 존재하지 않는데, 이경우에는 (+)연산을 해줘서
	--좌항 우항 COLUMN 값을 맞춘다.
	GROUP BY B.DEPTNO, B.DNAME
	ORDER BY 1;
	
	
	SELECT
		*
	FROM EMP
	WHERE 1=1
	WHERE 절이 TRUE일때 DB 동작한다.
	WHERE 1=1은 무조건 동작한다.
	
	AND EMPNO = 7369
	AND ENAME = 'SMITH';
	
아래는 SELECT 쿼리의 순서를 요약해놓은 것이다. 해당 쿼리 순서에 대한 자세한 내용은 참고문헌에 기재한다.

![SELECT_ORDER]({{"assets/img/oracle/select_order.png" | relative_url}})
*SELECT 쿼리의 진행 순서*

![SELECT_ORDER_DETAIL]({{"assets/img/oracle/select_order_detail.png" | relative_url}})
*테이블로 시각화된 SELECT 쿼리 진행 순서*

*참고문헌  
[마이자몽님 블로그] <https://myjamong.tistory.com/172>*
