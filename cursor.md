### 오라클 커서(cursor)란?
SQL문을 처리한 결과 집합을 가리키는 일종의 포인터  
SQL을 처리한 결과를 Private SQL Area 라는 곳에 가리키고 있다.  
오라클은 모든 처리결과를 커서 단위로 처리중.  
### Cursor 사용이유?
결과가 여러행 일때 이를 저장하기 위해
SELECT INTO가 한 줄만 저장할 때 Cursor는 여러줄을 저장.  
따라서 LOOP문과 동시에 사용되는 것이 보통이다.
### 암시적 커서
아래는 암시적 커서의 속성
* SQL%FOUND     : 해당 SQL문에 의해 반환된 총 행수가 1개 이상일 경우TRUE (BOOLEAN)
* SQL%NOTFOUND : 해당 SQL문에 의해 반환된 총 행수가 없을 경우 TRUE (BOOLEAN)
* SQL%ISOPEN     : 항상 FALSE, 암시적 커서가 열려 있는지의 여부 검색( PL/SQL은 실행 후 바로 묵시적 커서를 닫기 때문에 항상 상 false)
* SQL%ROWCOUNT : 해당 SQL문에 의해 반환된 총 행수, 가장 최근 수행된 SQL문에 의해 영향을 받은 행의 갯수(정수)
명시적 커서에서도 사용 가능
### 명시적 커서
사용자가 선언해서 사용하는 사용자 정의 커서라고 볼 수 있다.  
커서 선언 - 커서 열기 - 패치 단계에서 커서 사용 - 커서 닫기 의 단계를 거침
```
DECLARE
--    선언부
    v_emp_name EMPLOYEES.EMP_NAME%TYPE; 
    v_emp_hiredate EMPLOYEES.HIRE_DATE%TYPE;
    
--    커서 선언
    CURSOR cur_emp
    IS
    SELECT EMP_NAME, HIRE_DATE FROM employees WHERE employee_id between 100 and 150;
BEGIN 
--    커서 열기 / 패치
    OPEN cur_emp();
    LOOP
      FETCH cur_emp INTO v_emp_name, v_emp_hiredate;
    EXIT WHEN cur_emp%NOTFOUND;
    DBMS_OUTPUT.PUT_LINE(v_emp_name);
    DBMS_OUTPUT.PUT_LINE(v_emp_hiredate);
  END LOOP;
  
    DBMS_OUTPUT.PUT_LINE(cur_emp%ROWCOUNT);
    IF cur_emp%ISOPEN THEN DBMS_OUTPUT.PUT_LINE('cursor is opened');
    ELSE DBMS_OUTPUT.PUT_LINE('cursor is closed');
    END IF;
  
--  커서 닫기
  CLOSE cur_emp;
  
   IF cur_emp%ISOPEN THEN DBMS_OUTPUT.PUT_LINE('cursor is opened');
    ELSE DBMS_OUTPUT.PUT_LINE('cursor is closed');
    END IF;
END;

```
다음과 같이 FOR LOOP 문을 사용하면 OPEN, FETCH CLOSE 자동으로 한다.
```
DECLARE
CURSOR emp_cur IS
    select employee_id, last_name from employees;
BEGIN
-- FOR문과 Cursor가 같이사용될 때는
    FOR emp_rec IN emp_cur LOOP -- 명시적 커서의 OPEN,FETCH가 자동으로 수행
        DBMS_OUTPUT.PUT_LINE(emp_rec.employee_id||' '||emp_rec.last_name);
    END LOOP; -- 루프문 빠져나갈 때 자동적으로 커서가 CLOSE됨
END;
```
### 커서변수
커서를 변수처럼 사용하여 재사용이 가능하도록 하는 것.  
강한 변수 타입(int, char과 같은)은 return 타입을 정의  
약한 변수 타입(javascript의 var와 같은)은 return 타입을 생략한다.  
이하는 커서변수 선언 문법이다.
```
TYPE 커서_타입명 IS REF CURSOR [ RETURN 반환타입 ];
커서_변수명 커서_타입명;
```
이하는 '강한 커서타입' 선언 예시
```
TYPE dep_curtype IS REF CURSOR RETURN department%ROWTYPE;
dep_curvar dep_curtype
```
이하는 '약한 커서타입' 선언 예시
```
TYPE dep_curtype IS REF CURSOR;
dep_curvar dep_curtype;
```
약결합일때는 SYS_REFCURSOR을 사용하면 굳이 두 줄을 사용할 필요없이 간략하게 사용이 가능하다.
```
dep_curvar SYS_REFCURSOR;
```
### 커서 표현식
SELECT문에서 컬럼 형태로 커서를 사용 하는 것.  
아래와 같이 사용한다.
```
CURSOR( 서브쿼리 )
```
일반적으로 다중행이 리턴되는 쿼리를 select 문에 사용할 경우 ORA-01427 오류를 낸다.  
그렇지만 cursor를 사용하면 해당 row가 출력되는 다중행을 '가리키도록' 하여 출력이 가능함.  
