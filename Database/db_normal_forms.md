
# 정규화

- 데이터 중복을 최소화하고 데이터 무결성을 유지하기 위한 데이터베이스 설계 기법
- 데이터를 구조적으로 체계화하여 이상 발생을 방지함

### 필요성

- 데이터 중복 제거
- 데이터 무결성 유지
- 데이터베이스 저장 공간 최적화
- 삽입, 갱신, 삭제 이상 방지

---

## 정규형(Normal Forms)

### 제1정규형(1NF: First Normal Form)

- 하나의 칸에는 하나의 값만
- 모든 컬럼이 원자값(Atomic Value)을 가져야 함

#### **정규화 전**

| 고객ID | 이름 | 전화번호 |
| --- | --- | --- |
| 1 | 홍길동 | 010-1234-5678, 02-9876-5432 |

#### 1NF 변환

| 고객ID | 이름 | 전화번호 |
| --- | --- | --- |
| 1 | 홍길동 | 010-1234-5678 |
| 1 | 홍길동 | 02-9876-5432 |

---

### 제2정규형(2NF: Second Normal Form)

- 중요한 정보는 자기 테이블에
- 1NF를 만족하면서, 부분 함수 종속 제거

#### 1NF

| 주문ID | 고객ID | 고객이름 | 상품ID | 상품명 |
| --- | --- | --- | --- | --- |
| 101 | 1 | 홍길동 | A1 | 모니터 |
| 102 | 2 | 이순신 | B2 | 키보드 |

#### 2NF (고객과 주문 테이블 분리)

| 고객ID | 고객이름 |
| --- | --- |
| 1 | 홍길동 |
| 2 | 이순신 |

| 주문ID | 고객ID | 상품ID |
| --- | --- | --- |
| 101 | 1 | A1 |
| 102 | 2 | B2 |

| 상품ID | 상품명 |
| --- | --- |
| A1 | 모니터 |
| B2 | 키보드 |

---

### 제3정규형(3NF: Third Normal Form)

- 중간 정보는 따로 정리
- 2NF를 만족하면서, 이행적 함수 종속 제거

#### 2NF (고객 주소 테이블 분리)

| 주문ID | 고객ID | 고객주소 |
| --- | --- | --- |
| 101 | 1 | 서울시 강남구 |
| 102 | 2 | 부산시 해운대구 |

#### 3NF 

| 고객ID | 고객주소 |
| --- | --- |
| 1 | 서울시 강남구 |
| 2 | 부산시 해운대구 |

| 주문ID | 고객ID |
| --- | --- |
| 101 | 1 |
| 102 | 2 |

---

### BCNF(Boyce-Codd Normal Form)

- 3NF를 만족하면서 모든 결정자가 후보 키

#### **3NF**

| 교수ID | 강의실 | 과목 |
| --- | --- | --- |
| P1 | R101 | 수학 |
| P1 | R102 | 물리 |
| P2 | R201 | 화학 |

관계 1. `교수ID → 과목` 

관계 2. `강의실 → 교수ID`

특정 강의실에서 특정 교수만 강의한다는 제약이 있다고 할 때, **강의실**은 기본 키가 아님에도 **결정자 역할**을 한다. 

#### 왜 분리해야 할까?

- 강의실이 결정자 역할을 하지만 **후보 키가 아님**
- BCNF **규칙에 위반됨**

#### BCNF

강의 배정 테이블

| 교수ID | 과목 |
| --- | --- |
| P1 | 수학 |
| P1 | 물리 |
| P2 | 화학 |

강의실 배정 테이블

| 강의실 | 교수ID |
| --- | --- |
| R101 | P1 |
| R102 | P1 |
| R201 | P2 |

`강의실 → 교수ID` 종속성 제거

**모든 결정자가 후보 키**.

---

### 제4정규형(4NF: Fourth Normal Form)

- BCNF를 만족하면서 **다중 값 종속 제거**

다중 값 종속
- 한 개의 키 속성이 두 개 이상의 독립적인 속성과 다대다 관계를 가질 때 발생
- 한 테이블에서 한 속성이 특정되었을 때, 다른 속성이 독립적으로 여러 값을 가질 수 있는 경우 문제가 됨

#### BCNF + 다중 값 종속

| 학생ID | 수강 과목 | 동아리 |
| --- | --- | --- |
| S1 | 수학 | 축구 |
| S1 | 수학 | 야구 |
| S1 | 영어 | 축구 |
| S1 | 영어 | 야구 |

관계 1. `학생ID → 수강 과목` (한 학생이 여러 과목을 수강 가능)

관계 2. `학생ID → 동아리` 관계도 있음 (한 학생이 여러 동아리 가입 가능)

수강 과목과 동아리는 서로 관계x. 만약 학생이 동아리를 변경하면 수강 과목까지 함께 수정해야 함


#### 4NF

학생-수강 과목 테이블

| 학생ID | 수강 과목 |
| --- | --- |
| S1 | 수학 |
| S1 | 영어 |

학생-동아리 테이블

| 학생ID | 동아리 |
| --- | --- |
| S1 | 축구 |
| S1 | 야구 |

`학생ID → 수강 과목` / `학생ID → 동아리`가 **독립적으로 관리**

---

### 제5정규형(5NF: Fifth Normal Form)
- 다대다 관계가 여러 개 얽힌 경우
- 조인으로 인한 불필요한 중복 제거
- 조인을 줄이고 불필요한 분해를 방지해서 최적의 테이블 구조를 찾기

#### 조인 종속
- 하나의 릴레이션을 여러개의 릴레이션으로 분해하였다가, 다시 조인했을 때 데이터 손실이 없는 것
- 조인 종속성이 만족되면, 데이터를 분해한 후 다시 조인해도 원래 데이터를 복원할 수 있음

#### 5NF가 필요한 이유
4NF까지 진행하면 **모든 다중 값 종속이 제거**되어 중복이 최소화되지만 테이블이 과하게 분해되면서, 조인 시 불필요한 중복이 발생할 수 있음


#### 강의 배정 시스템 (4NF + 조인 중복 발생)

| 학생ID | 과목ID | 교수ID |
| --- | --- | --- |
| S1 | C1 | P1 |
| S1 | C1 | P2 |
| S1 | C2 | P1 |
| S2 | C1 | P1 |
| S2 | C2 | P2 |

- 학생(S) → 과목(C),
- 학생(S) → 교수(P),
- 과목(C) → 교수(P)

#### 테이블 분리

1. **학생 - 과목**
    
    | 학생ID | 과목ID |
    |---|---|
    | S1 | C1 |
    | S1 | C2 |
    | S2 | C1 |
    | S2 | C2 |
    
2. **과목 - 교수**
    
    | 과목ID | 교수ID |
    |---|---|
    | C1 | P1 |
    | C1 | P2 |
    | C2 | P1 |
    | C2 | P2 |
    
3. **학생 - 교수**
    
    | 학생ID | 교수ID |
    |---|---|
    | S1 | P1 |
    | S1 | P2 |
    | S2 | P1 |
    | S2 | P2 |


- **S1이 C1을 P1에게 듣는 것인지 P2에게 듣는 것인지 알 수 없음**
- 학생이 어떤 교수에게 어떤 과목을 듣는지 정보가 유실

결과적으로, 원래 테이블 구조에서 조인으로 인한 중복 문제를 해결하는 5NF로 데이터 분해가 필요

| 학생ID | 과목ID | 교수ID |
| --- | --- | --- |
| S1 | C1 | P1 |
| S1 | C1 | P2 |
| S1 | C2 | P1 |
| S2 | C1 | P1 |
| S2 | C2 | P2 |



https://superohinsung.tistory.com/111

https://velog.io/@wg_cat/%EC%A0%9C-15%EC%A0%95%EA%B7%9C%ED%99%94-%EA%B8%B0%EB%B2%95

https://velog.io/@wisdom-one/%EC%A0%95%EA%B7%9C%ED%99%94Normalization

https://www.geeksforgeeks.org/difference-between-4nf-and-5nf/
