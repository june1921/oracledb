ibatis에서 like 구문 사용법은 일반적인 sql문과는 다르다.
EX)
```
UPPER(A.EMPNO) LIKE '%' || UPPER(#EMPNO#) || '%')
```
