# Query Optimization(Slow Query Tuning)

데이터베이스의 성능을 높이기 위해 SQL 쿼리를 분석하고 최적화하는 과정



## **슬로우 쿼리 원인**

1. **인덱스 미사용 또는 잘못된 인덱스**
    - 적절한 인덱스가 없거나 과도한 인덱스를 사용하면 쿼리 성능이 저하
2. **불필요한 풀 테이블 스캔**
    - 적은 양의 데이터를 사용할 경우, 풀 테이블 스캔을 할 경우 성능 저하
3. **잘못된 조인 (JOIN) 순서 및 방식**
    - 조인의 순서나 방식에 따라 성능 차이가 큼
4. **과한 서브쿼리 사용**
    - 서브쿼리는 JOIN, EXISTS, IN으로 변경하는 것이 더 효율적일 수 있음
5. **트랜잭션 및 락 대기**
    - 긴 트랜잭션이 다른 쿼리들의 대기 상태를 유발할 수 있으므로 트랜잭션을 최소화하고 적절한 커밋 시점을 설정해야 함

# **쿼리 최적화**

![](/Database/img/db_query_optimization_1.png)

## **1. 실행 계획(EXPLAIN, EXECUTION PLAN) 확인**

**실행 계획을 분석하여 쿼리 성능을 점검**할 수 있다. 

- **MySQL:** `EXPLAIN SELECT ...`
- **Oracle/Tibero:** `EXPLAIN PLAN FOR SELECT ...`
- **PostgreSQL:** `EXPLAIN ANALYZE SELECT ...`

```sql
EXPLAIN PLAN FOR
SELECT /*+ full(user_id)*/ * FROM T_USER
WHERE user_id= 'USER000502'

SELECT 
	SQL_ID
	, OPERATION
	, OBJECT_NAME
	, SEARCH_COLUMNS
	, COST
FROM PLAN_TABLE
WHERE SQL_ID IN ( '51s88agb5q2fw', '5xfxqjnpxhuvw' )
```

![](/Database/img/db_query_optimization_2.png)



## **2. 인덱스 최적화**

✅ 

- WHERE, JOIN, ORDER BY, GROUP BY에서 인덱스를 제대로 활용하고 있는가?
- 불필요한 인덱스가 오히려 성능 저하를 유발하지 않는가?
- 인덱스가 많은 경우 유지 관리 비용을 고려했는가?
- 예상치 못한 TABLE FULL SCAN이 발생하지 않는가?
- 적절한 인덱스 사용으로 풀스캔을 줄였는가?

### **인덱스 최적화 목표**

- 스캔 과정에서 발생하는 비효율 줄이기
- 테이블 액세스 회수 줄이기

```sql
SELECT * FROM T_USER
WHERE user_id= 'USER000502'
// 조건에 해당하는 데이터가 한 건이므로 인덱스를 사용하는 방식이 좋다.

SELECT * FROM T_USER
WHERE 유저구분코드 = 'a001'
// 유저구분코드가 'a001'인 고객 데이터가 100만 건일 때, 풀 스캔 방식이 효과적이다.
```

인덱스는 큰 테이블에서 소량 데이터를 검색할 때 사용한다. (목차)
    ![](/Database/img/db_query_optimization_3.png)

index range scan 을 할 때는 데이터가 정렬되어 있어야 하고 스캔 시작점과 종료점(인덱스 블록에서 스캔하는 양)이 중요

✅ **적절한 인덱스 추가**

```sql
CREATE INDEX idx_user_name ON T_USER(name);
```

- 자주 조회되는 컬럼이나 **조인 조건에 사용되는 컬럼**에 인덱스를 추가

✅ **복합 인덱스**

```sql
CREATE INDEX idx_orders_user_date ON T_ORDER(user_id, order_date);
```

- WHERE 절에 다중 컬럼을 사용할 경우 사용
- 인덱스 순서도 중요
    - `WHERE user_id = 1 AND order_date = '2024-03-01'`
        - 인덱스 사용 가능
        - user_id로 스캔 범위를 좁힐 수 있음
    - `WHERE order_date = '2024-03-01'`
        - 모든 데이터를 조회해야 할 수 있음
        



## **3. JOIN 최적화**

✅ 

- INNER JOIN이 OUTER JOIN보다 적절한가?
- 조인 순서를 변경하면 성능이 향상되는가?
- 조인 대상 테이블에 적절한 인덱스가 설정되어 있는가?

### **NL(Nested Loops) 조인**

- 인덱스를 이용한 조인
- 작은 테이블을 먼저 읽고, 그 값으로 큰 테이블을 조회
- 인덱스가 잘 활용되지 않으면 성능이 저하될 수 있음

```sql
SELECT a.이름, b.상품이름, b.주문일자
FROM T_USER a, T_ORDER b
WHERE a.user_id = b.user_id
AND a.가입일자 = '20250101'
```

### **NL 조인 동작 방식**

1. T_USER 테이블에서 데이터를 가져옴
    - `가입일자 = '20250101'` 조건을 만족하는 유저 조회
2. 각 유저의 `user_id`로 T_ORDER 테이블을 검색
    - T_USER 에서 가져온 `user_id`를 이용해 `T_ORDER` 테이블에서 해당 유저의 주문 데이터를 조회
3. 반복
    - 위 과정을 모든 유저에 대해 반복 수행
        
        ![](/Database/img/db_query_optimization_4.png)
        

### **실행 계획**

| 단계 | 연산(Operation) | 테이블 | 액세스 방식(Access Method) |
| --- | --- | --- | --- |
| 1 | TABLE ACCESS BY INDEX ROWID | T_USER | `가입일자` 필터링 (INDEX 사용) |
| 2 | INDEX RANGE SCAN | idx_user_join_date | `가입일자` 인덱스 탐색 |
| 3 | NESTED LOOPS |  |  |
| 4 | TABLE ACCESS BY INDEX ROWID | T_ORDER | `user_id` 인덱스를 통한 검색 |
| 5 | INDEX UNIQUE SCAN | idx_order_user_id | `user_id` 인덱스를 사용한 검색 |
- T_USER에서 먼저 가입일자 조건을 필터링 (`INDEX RANGE SCAN`)
- 그 결과를 기반으로 T_ORDER를 NESTED LOOPS 방식으로 반복 조회

✅ **조인 조건에 인덱스 추가**

```sql
CREATE INDEX idx_order_user_id ON T_ORDER(user_id);
```

- `T_ORDER.user_id`에 인덱스를 추가하면 `INDEX UNIQUE SCAN`이 발생하며 빠르게 조인 가능

✅ **드라이빙 테이블 크기 줄이기**

```sql
CREATE INDEX idx_user_join_date ON T_USER(가입일자);
```

- 필터링 조건이 먼저 최적화되어야 `T_USER`의 크기가 줄어들어 NL 조인의 반복 횟수가 감소함

## **Hash Join**

두 개의 테이블 중 **작은 테이블을 메모리에 해시 테이블로 저장**한 후, 큰 테이블을 스캔하면서 해시 테이블과 조인하는 방식

1. NL 조인이 비효율적일 때 (인덱스를 활용하지 못할 때)
    - NL 조인은 데이터가 적을 때는 효율적이지만, **대량 데이터 조인 시 비효율적**일 수 있음
2. 드라이빙 테이블이 작을 때

### **Hash Join동작 방식**

1. **Build**
    - 작은 테이블을 메모리에 올려 **해시 테이블을 생성**
    - 조인 키를 기준으로 해시 값을 계산하여 **버킷**에 저장.
2. **Probe**
    - 큰 테이블을 읽으면서 해시 테이블을 참조하여 매칭되는 행을 탐색
    

```sql
SELECT /*+ USE_HASH(b) */ a.이름, b.상품이름, b.주문일자
FROM T_USER a, T_ORDER b
WHERE a.user_id = b.user_id
AND a.가입일자 = '20250101';
```

1. **`T_USER`(작은 테이블)에서 `가입일자 = '20250101'` 필터링**
    
    ```sql
    SELECT user_id, 이름
    FROM T_USER
    WHERE 가입일자 = '20250101';
    ```
    
    - 이 결과를 해시 테이블로 메모리에 저장
    
2. **`T_ORDER`(큰 테이블)를 스캔하며 `user_id`를 해시 테이블과 비교**
    
    ```sql
    SELECT 상품이름, 주문일자
    FROM T_ORDER
    WHERE user_id IN (해시 테이블);
    ```
    
    - `T_ORDER.user_id`를 해시 테이블에서 찾으며 빠르게 조인

✅ **드라이빙 테이블(작은 테이블) 크기를 줄이기**

```sql
SELECT /*+ USE_HASH(b) */ a.이름, b.상품이름, b.주문일자
FROM (SELECT user_id, 이름 FROM T_USER WHERE 가입일자 = '20250101') a
JOIN T_ORDER b
ON a.user_id = b.user_id;
```

- `T_USER`에서 미리 필터링하여 **해시 테이블 크기를 줄임** → 더 빠른 해시 매칭 가능.

✅ **해시 테이블이 너무 클 경우, Hash Partitioning 사용**

```sql
ALTER TABLE T_ORDER PARTITION BY HASH (user_id);
```

- 해시 파티셔닝을 통해 데이터 분산 → 메모리 사용 최적화.

---

[친절한SQL튜닝](https://www.yes24.com/product/search?query=%25EC%25B9%259C%25EC%25A0%2588%25ED%2595%259CSQL%25ED%258A%259C%25EB%258B%259D)

https://escapefromcoding.tistory.com/777

[https://bommbom.tistory.com/entry/ROWID와-클리스터링-팩터CF의-관계는](https://bommbom.tistory.com/entry/ROWID%EC%99%80-%ED%81%B4%EB%A6%AC%EC%8A%A4%ED%84%B0%EB%A7%81-%ED%8C%A9%ED%84%B0CF%EC%9D%98-%EA%B4%80%EA%B3%84%EB%8A%94)

[https://velog.io/@yooha9621/SQLP4장-인덱스와-조인-3.-조인-기본원리](https://velog.io/@yooha9621/SQLP4%EC%9E%A5-%EC%9D%B8%EB%8D%B1%EC%8A%A4%EC%99%80-%EC%A1%B0%EC%9D%B8-3.-%EC%A1%B0%EC%9D%B8-%EA%B8%B0%EB%B3%B8%EC%9B%90%EB%A6%AC)

