ibatis에서 like 구문 사용법은 일반적인 sql문과는 다르다.
EX)
```
UPPER(A.EMPNO) LIKE '%' || UPPER(#EMPNO#) || '%')
```
ibatis에서는 꺽쇠를 부호로 인식하기 때문에 다음과 같은 형식으로 감싸줘야 한다.
```
<![CDATA[ AND YEAR >= #dsSearch.YEAR# ]]>
```
