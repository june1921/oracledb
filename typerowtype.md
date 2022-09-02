### %TYPE
컬럼 값을 참조하는 데이터 타입
```
TNAME EMPLOYEES.FIRST_NAME%TYPE;
```

### %ROWTYPE
테이블 자체를 참조하는 데이터 타입
```
TEMP EMPLOYEES%ROWTYPE;
```
### 용도
PL/SQL에서 자주 값이 변하는 경우 참조형으로 만들어 데이터 타입과 크기를 지속적으로 변경 안해줘도 된다는 장점이 있음.
