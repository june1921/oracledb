### WITH절 사용법
```
WITH WITH절이름 AS (

... QUERY ...

)

SELECT * FROM WITH절이름
```
이름이 부여된 서브쿼리로 VIEW와 비슷하지만 WITH절은 한 쿼리문 내에서 한번 선언되고 여러번 쓰여질수 있으며 쿼리문이 종료되면 버려진다.
코드의 재사용성을 높이기 위해서 쓰인다.  

### CONNECT BY 함수
ORACLE에서는 계층 데이터를 위해 CONNECT BY절을 지원한다.  
자세한 학습은 나중에

#### CONNECT BY LEVEL 
연속된 숫자를 조회할 때 활용.
```
SELECT LEVEL AS NO
  FROM DUAL
CONNECT BY LEVEL <= 10
```

