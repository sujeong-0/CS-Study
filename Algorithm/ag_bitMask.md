## 비트마스크

- 정수를 이진수(비트) 형태로 표현하여, 비트 연산을 활용해 데이터를 효율적으로 관리하는 기법
- 주로 집합을 표현하거나 상태를 압축하여 저장하는 데 사용됨

### 비트 연산

| 연산 | 설명 | 예시 |
| --- | --- | --- |
| **AND (`&`)** | 공통된 비트가 1이면 1 | `1010 & 1100 = 1000` |
| **OR** (`\|`) | 하나라도 1이면 1 | `1010 \| 1100 = 1110` |
| **XOR (`^`)** | 다르면 1, 같으면 0 | `1010 ^ 1100 = 0110` |
| **NOT (`~`)** | 0은 1로, 1은 0으로 | `~1010 = 0101` |
| **SHIFT (`<<`, `>>`)** | 비트를 좌/우로 이동 | `1010 << 1 = 10100` / `1010 >> 1 = 01010` |
| `\|=` | **OR** 대입 연산자 (둘 중 하나라도 1이면 1) | `a \|= b` → `a = a \| b` |
| **`&=`** | **AND** 대입 연산자 (둘 다 1이어야 1) | `a &= b` → `a = a & b` |
| **`^=`** | **XOR** 대입 연산자 (서로 다를 때 1) | `a ^= b` → `a = a ^ b` |
| **`+=`** | 덧셈 대입 연산자 | `a += b` → `a = a + b` |
| **`-=`** | 뺄셈 대입 연산자 | `a -= b` → `a = a - b` |
| **`*=`** | 곱셈 대입 연산자 | `a *= b` → `a = a * b` |
| **`/=`** | 나눗셈 대입 연산자 | `a /= b` → `a = a / b` |
| **`%=`** | 나머지 대입 연산자 | `a %= b` → `a = a % b` |

### 1. 집합 표현

- N개의 요소가 있는 집합을 N비트 정수로 표현
    - `{0, 2, 3}`을 비트마스크로 나타내면 `1101 (2^3 + 2^2 + 2^0)`
- O(1)의 시간 복잡도로 집합 연산 가능
    
    ```
    set = {0, 2, 3} = 1101 (2^3 + 2^2 + 2^0)
    
    int set = 0; // 공집합
    set |= (1 << 0); // {0} 추가 -> 0001
    set |= (1 << 2); // {0,2} 추가 -> 0101
    set |= (1 << 3); // {0,2,3} 추가 -> 1101
    ```
    

### 2. 특정 원소 포함 여부 확인

- 특정 원소가 집합에 포함되었는지 `AND` 연산으로 확인
    
    ```
    set = 1101
    
    if ((set & (1 << 2)) != 0) {
        System.out.println("2가 포함됨");
    }
    
    // (1 << 2) = 0100 
    ```
    

### 3. 원소 삭제

- 특정 원소를 제거할 때 `AND` 연산과 `NOT`을 활용
    
    ```
    set = 1101
    set &= ~(1 << 2);
    
    // ~(1 << 2) = 1011
    // set = 1001
    ```
    

### 4. 원소 토글(있으면 제거, 없으면 추가)

- `XOR` 연산을 사용하면 특정 원소를 추가 또는 제거 가능
    
    ```
    set = 1101
    set ^= (1 << 2);
    
    // (1 << 2) = 0100
    // set = 1010
    ```
    

### 5. 모든 원소가 포함된 상태(전체 집합)

- N개의 비트가 모두 1인 값
    
    ```
    int N = 3;
    int fullSet = (1 << N) - 1; // 0111
    
    // (1 << 3) = 1000
    ```
    

---

## 활용 예제

### 1. 부분 집합 탐색

```java
int n = 3;
for (int subset = 0; subset < (1 << n); subset++) {
    System.out.print("Subset: ");
    for (int i = 0; i < n; i++) {
        if ((subset & (1 << i)) != 0) { 
            // subset의 i번째 비트가 1인지 0인지 확인
            System.out.print(i + " ");
        }
    }
    System.out.println();
}
```

| **subset (이진수)** | **subset 값** | **출력** |
| --- | --- | --- |
| 000 | 0 | Subset: |
| 001 | 1 | Subset: 0 |
| 010 | 2 | Subset: 1 |
| 011 | 3 | Subset: 0 1 |
| 100 | 4 | Subset: 2 |
| 101 | 5 | Subset: 0 2 |
| 110 | 6 | Subset: 1 2 |
| 111 | 7 | Subset: 0 1 2 |

### 2. DP 예제

```java
int[][] dist = new int[N][N]; // 도시 i에서 도시 j까지의 거리
int[][] dp = new int[1 << N][N]; // (방문 상태, 현재 노드)

int tsp(int visited, int cur) {
    if (visited == (1 << N) - 1) return dist[cur][0]; // 모든 도시 방문 완료
    if (dp[visited][cur] != -1) return dp[visited][cur]; // 현재 상태에서 최단 경로를 저장

    int result = Integer.MAX_VALUE;
    for (int next = 0; next < N; next++) {
        if ((visited & (1 << next)) == 0) { // 아직 방문 안한 도시
		        // result에는 최단 경로가 갱신
            result = Math.min(result, dist[cur][next] + tsp(visited | (1 << next), next));
        }
    }
    return dp[visited][cur] = result;
}
```


---

### 비트마스크의 장점

- **메모리 절약 :** 배열보다 작은 공간을 사용해 상태를 저장
- **빠른 연산  O(1) :** 일반적인 연산보다 빠르고 메모리 공간을 절약할 수 있다.
- **알고리즘 최적화** : 간단하고 빠르게 DP, 그래프 탐색 등 다양한 문제 해결 가능

### 언제 사용하면 좋을까?

- 집합 연산이 빈번한 경우
- DP에서 상태를 효율적으로 관리할 때
- 그래프에서 방문 여부를 체크할 때

---

https://rebro.kr/63

https://mygumi.tistory.com/361

https://david0506.tistory.com/76
