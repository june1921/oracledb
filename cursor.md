### 오라클 커서(cursor)란?
SQL문을 처리한 결과 집합을 가리키는 일종의 포인터  
SQL을 처리한 결과를 Private SQL Area 라는 곳에 가리키고 있다.  
오라클은 모든 처리결과를 커서 단위로 처리중.  
### Cursor 사용이유?
결과가 여러행 일때 이를 저장하기 위해
SELECT INTO가 한 줄만 저장할 때 Cursor는 여러줄을 저장.
### 암시적 커서
아래는 암시적 커서의 속성
* SQL%FOUND     : 해당 SQL문에 의해 반환된 총 행수가 1개 이상일 경우TRUE (BOOLEAN)
* SQL%NOTFOUND : 해당 SQL문에 의해 반환된 총 행수가 없을 경우 TRUE (BOOLEAN)
* SQL%ISOPEN     : 항상 FALSE, 암시적 커서가 열려 있는지의 여부 검색( PL/SQL은 실행 후 바로 묵시적 커서를 닫기 때문에 항상 상 false)
* SQL%ROWCOUNT : 해당 SQL문에 의해 반환된 총 행수, 가장 최근 수행된 SQL문에 의해 영향을 받은 행의 갯수(정수)
명시적 커서에서도 사용 가능
### 명시적 커서
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