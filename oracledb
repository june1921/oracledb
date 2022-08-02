레코드 관련 정리
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
