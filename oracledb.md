# PL/SQL 관련 정리
```
/* RECORD를 삽입할 테이블 생성 */
CREATE TABLE DEPT_RECORD
AS SELECT * FROM DEPT;

/* RECORD 선언 및 INSERT문 실행 */
DECLARE
    TYPE REC_DEPT IS RECORD(
        deptno NUMBER(2) NOT NULL := 99,
        dname DEPT.DNAME%TYPE,
        loc DEPT.LOC%TYPE
    );
    dept_rec REC_DEPT;
BEGIN
    dept_rec.deptno := 99;
    dept_rec.dname := 'DATABASE';
    dept_rec.loc := 'SEOUL';
    
    INSERT INTO DEPT_RECORD
    VALUES dept_rec;
END;

/* INSERT된 RECORD 조회 */    
SELECT * FROM DEPT_RECORD;

/* UPDATE 실행 */
DECLARE
    TYPE REC_DEPT IS RECORD(
        deptno NUMBER(2) NOT NULL := 99,
        dname DEPT.DNAME%TYPE,
        loc DEPT.LOC%TYPE
    );
    dept_rec REC_DEPT;
BEGIN
    dept_rec.deptno := 50;
    dept_rec.dname := 'DB';
    dept_rec.loc := 'BUSAN';
    
    UPDATE DEPT_RECORD
    SET ROW = dept_rec
    WHERE DEPTNO= 99;
END;

/* UPDATE된 테이블 조회 */
SELECT * FROM DEPT_RECORD;

/* 연관 배열 COLLECTION 생성 */
DECLARE
    TYPE ITAB_EX IS TABLE OF VARCHAR2(20)
    INDEX BY PLS_INTEGER;
    text_arr ITAB_EX;
    
BEGIN
    text_arr(1) := '1st data';
    text_arr(2) := '2st data';
    text_arr(3) := '3st data';
    text_arr(4) := '4st data';
    
    DBMS_OUTPUT.PUT_LINE('text_arr(1): ' || text_arr(1));
    DBMS_OUTPUT.PUT_LINE('text_arr(2): ' || text_arr(2));
    DBMS_OUTPUT.PUT_LINE('text_arr(3): ' || text_arr(3));
    DBMS_OUTPUT.PUT_LINE('text_arr(4): ' || text_arr(4));
END;

/* 레코드를 활용한 연관 배열 */
DECLARE
    TYPE REC_DEPT IS RECORD(
        deptno DEPT.DEPTNO%TYPE,
        dname DEPT.DNAME%TYPE
    );
    
    TYPE ITAB_DEPT IS TABLE OF REC_DEPT
    INDEX BY PLS_INTEGER;
    
    dept_arr ITAB_DEPT;
    idx PLS_INTEGER := 0;
    
BEGIN
    FOR i IN ( SELECT DEPTNO, DNAME FROM DEPT) LOOP
    idx := idx + 1;
    dept_arr(idx).deptno := i.DEPTNO;
    dept_arr(idx).dname := i.DNAME;
    
    DBMS_OUTPUT.PUT_LINE(
        dept_arr(idx).deptno || ': ' || dept_arr(idx).dname);
    END LOOP;
END;

/* ROWTYPE을 활용한 특정 테이블의 전체 열과 같은 구성을 가진 연관 배열 */
DECLARE
    TYPE ITAB_DEPT IS TABLE OF DEPT%ROWTYPE
    INDEX BY PLS_INTEGER;
    
    dept_arr ITAB_DEPT;
    idx PLS_INTEGER := 0;
    
BEGIN
    FOR i IN ( SELECT * FROM DEPT) LOOP
    idx := idx + 1;
    dept_arr(idx).deptno := i.DEPTNO;
    dept_arr(idx).dname := i.DNAME;
    dept_arr(idx).loc := i.LOC;
    
    DBMS_OUTPUT.PUT_LINE(
        dept_arr(idx).deptno || ': ' ||
        dept_arr(idx).dname || ': ' ||
        dept_arr(idx).loc);
    END LOOP;
END;

/* CURSOR, SELECT INTO를 사용한 단일행 데이터 저장하기 */
DECLARE
    V_DEPT_ROW DEPT%ROWTYPE;
BEGIN
    SELECT DEPTNO, DNAME, LOC INTO V_DEPT_ROW
      FROM DEPT
     WHERE DEPTNO = 40;
    DBMS_OUTPUT.PUT_LINE('DEPTNO: ' || V_DEPT_ROW.DEPTNO);
    DBMS_OUTPUT.PUT_LINE('DNAME: ' || V_DEPT_ROW.DNAME);
    DBMS_OUTPUT.PUT_LINE('LOC: ' || V_DEPT_ROW.LOC);
END;

/* 여러 행의 데이터를 커서에 저장하여 사용하기(LOOP) */
DECLARE
    V_DEPT_ROW DEPT%ROWTYPE;
    
    CURSOR c1 IS
        SELECT DEPTNO, DNAME, LOC
          FROM DEPT;

BEGIN

    OPEN c1;
    
    LOOP
        FETCH c1 INTO V_DEPT_ROW;
        EXIT WHEN c1%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('DEPTNO: ' || V_DEPT_ROW.DEPTNO
                        || ', DNAME: '  || V_DEPT_ROW.DNAME
                        || ', LOC: '    || V_DEPT_ROW.LOC);
    END LOOP;
    
    CLOSE c1;
END;

/* FOR LOOP문을 활용하여 커서 사용하기 */
DECLARE

    CURSOR c1 IS
        SELECT DEPTNO, DNAME, LOC
          FROM DEPT;

BEGIN
    
    FOR c1_rec IN c1 LOOP
        DBMS_OUTPUT.PUT_LINE('DEPTNO: ' || c1_rec.DEPTNO
                        || ', DNAME: '  || c1_rec.DNAME
                        || ', LOC: '    || c1_rec.LOC);
    END LOOP;
    
END;

/* 파라미터를 사용하는 커서 */
DECLARE
    V_DEPT_ROW DEPT%ROWTYPE;
    
    CURSOR c1 (p_deptno DEPT.DEPTNO%TYPE) IS
        SELECT DEPTNO, DNAME, LOC
          FROM DEPT
         WHERE DEPTNO = p_deptno;
BEGIN

    OPEN c1(10);
    
    LOOP
        FETCH c1 INTO V_DEPT_ROW;
        EXIT WHEN c1%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('DEPTNO: ' || V_DEPT_ROW.DEPTNO
                        || ', DNAME: '  || V_DEPT_ROW.DNAME
                        || ', LOC: '    || V_DEPT_ROW.LOC);
    END LOOP;
    
    CLOSE c1;
    
    OPEN c1(20);
    
    LOOP
        FETCH c1 INTO V_DEPT_ROW;
        EXIT WHEN c1%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('DEPTNO: ' || V_DEPT_ROW.DEPTNO
                        || ', DNAME: '  || V_DEPT_ROW.DNAME
                        || ', LOC: '    || V_DEPT_ROW.LOC);
    END LOOP;
    
    CLOSE c1;
END;

/* 커서에 사용할 파라미터 입력받기 */
DECLARE

    v_deptno DEPT.DEPTNO%TYPE;
    
    CURSOR c1(p_deptno DEPT.DEPTNO%TYPE) IS
        SELECT DEPTNO, DNAME, LOC
          FROM DEPT
         WHERE DPETNO = p_deptno;

BEGIN
    
    v_deptno := &INPUT_DEPTNO;
    FOR c1_rec IN c1(v_deptno) LOOP
        DBMS_OUTPUT.PUT_LINE('DEPTNO: ' || c1_rec.DEPTNO
                        || ', DNAME: '  || c1_rec.DNAME
                        || ', LOC: '    || c1_rec.LOC);
    END LOOP;
    
END;

/* 묵시적 커서의 속성 사용하기 */

BEGIN
    UPDATE DEPT SET DNAME='DB'
      WHERE DEPTNO = 50;
    
    DBMS_OUTPUT.PUT_LINE('갱신된 행의 수: ' || SQL%ROWCOUNT);
    
    IF (SQL%FOUND) THEN
        DBMS_OUTPUT.PUT_LINE('갱신 대상 행 존재 여부: true');
    ELSE
        DBMS_OUTPUT.PUT_LINE('갱신 대상 행 존재 여부: false');
    END IF;
    
    IF (SQL%ISOPEN) THEN
        DBMS_OUTPUT.PUT_LINE('커서의 OPEN 여부: true');
    ELSE
        DBMS_OUTPUT.PUT_LINE('커서의 OPEN 여부: false');
    END IF;
    
END;

/* 예외처리 시행 */
DECLARE
    v_wrong NUMBER;
BEGIN
    SELECT DNAME INTO v_wrong
      FROM DEPT
     WHERE DEPTNO = 10;
     DBMS_OUTPUT.PUT_LINE('앞 부분이 예외 처리 된다면 뒷 부분은 실행되지 않음.');
EXCEPTION
    WHEN VALUE_ERROR THEN
        DBMS_OUTPUT.PUT_LINE('예외 처리: 수치 또는 값 오류 발생');
END;

/* 패키지 바디 */
CREATE OR REPLACE PACKAGE BODY pkg_example
IS
    body_no NUMBER := 10;
    
    FUNCTION func_aftertax(sal NUMBER) RETURN NUMBER
        IS
            tax NUMBER := 0.05;
        BEGIN
            RETURN (ROUND(sal - (sal * tax)));
    END func_aftertax;
    
    PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE)
        IS
            out_ename EMP.ENAME%TYPE;
            out_sal EMP.SAL%TYPE;
        BEGIN
            SELECT ENAME, SAL INTO out_ename, out_sal
              FROM EMP
             WHERE EMPNO = in_empno;
             
            DBMS_OUTPUT.PUT_LINE('ENAME: ' || out_ename);
            DBMS_OUTPUT.PUT_LINE('SAL: ' || out_sal);
            
    END pro_emp;
    
    PROCEDURE pro_dept(in_deptno IN DEPT.DEPTNO%TYPE)
        IS
            out_dname DEPT.DNAME%TYPE;
            out_loc DEPT.LOC%TYPE;
        BEGIN
            SELECT DNAME, LOC INTO out_dname, out_loc
              FROM DEPT
             WHERE DEPTNO = in_deptno;
             
            DBMS_OUTPUT.PUT_LINE('DNAME: ' || out_dname);
            DBMS_OUTPUT.PUT_LINE('LOC: ' || out_loc);
            
    END pro_dept;
    
END;

/* 패키지 오버로드 */
/* 패키지 바디 */
CREATE OR REPLACE PACKAGE BODY pkg_overload
IS

    PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE)
        IS
            out_ename EMP.ENAME%TYPE;
            out_sal EMP.SAL%TYPE;
        BEGIN
            SELECT ENAME, SAL INTO out_ename, out_sal
              FROM EMP
             WHERE EMPNO = in_empno;
             
            DBMS_OUTPUT.PUT_LINE('ENAME: ' || out_ename);
            DBMS_OUTPUT.PUT_LINE('SAL: ' || out_sal);
            
    END pro_emp;
    
    PROCEDURE pro_emp(in_ename IN EMP.ENAME%TYPE)
        IS
            out_ename EMP.ENAME%TYPE;
            out_sal EMP.SAL%TYPE;
        BEGIN
            SELECT ENAME, SAL INTO out_ename, out_sal
              FROM EMP
             WHERE ENAME = in_ename;
             
            DBMS_OUTPUT.PUT_LINE('ENAME: ' || out_ename);
            DBMS_OUTPUT.PUT_LINE('SAL: ' || out_sal);
            
    END pro_emp;
    
    
END;

/* 함수, 프로시저, 함수 오버로드 확인 */
BEGIN
    DBMS_OUTPUT.PUT_LINE('--pkg_example.func_aftertax(3000)--');
    DBMS_OUTPUT.PUT_LINE('after-tax: '|| pkg_example.func_aftertax(3000));
    
    DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_emp(7788)--');
    pkg_example.pro_emp(7788);
    
    DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_emp(7788)--');
    pkg_example.pro_dept(10);
    
    DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_emp(7788)--');
    pkg_overload.pro_emp(7788);
    
    DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_emp(''SCOTT'')--');
    pkg_overload.pro_emp('SCOTT');
    
END;
```
