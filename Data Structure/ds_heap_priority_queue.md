## 힙(Heap)

### 힙이란?
힙은 완전 이진 트리 기반의 자료 구조이며, 최소힙과 최대힙이 있고 종류에 따라 특징을 가집니다.

## 힙의 특징
- Heapify: 힙 데이터 구조의 속성을 유지하기 위해 노드를 재배치하는 프로세스입니다.
  - 삭제 연산: 루트 노드가 삭제되고, 가장 마지막 노드를 루트로 옮긴 다음 Heapify를 호출하여 힙 속성을 유지합니다.
  - 삽입 연산: 가장 마지막 노드부터 루트 노드까지 Heapify를 호출하여 힙 속성을 유지합니다.
- 완전 이진 트리: 모든 레벨이 완전하게 채워져 있어햐 하며, 마지막 레벨은 왼쪽부터 채워집니다.
- 효율적인 연산: 
  - 삽입/삭제: O(logN)
  - 최댓값/최솟값 조회: O(1)
- 최소힙: 부모 노드의 값이 자식 노드보다 작거나 같음
- 최대힙: 부모 노드의 값이 자식 노드보다 크거나 같음

### 힙의 삽입
1. 새 요소를 배열의 마지막에 추가합니다.
2. 부모와 비교하여 힙 속성을 유지하도록 상향 이동(Up-Heapify) 합니다.

![](/Data%20Structure/img/ds_heap_priority_queue_1.png)

### 힙의 삭제
1. 루트 노드(최댓값 또는 최솟값)를 제거합니다.
2. 배열의 마지막 요소를 루트로 이동합니다.
3. 자식과 비교하여 힙 속성을 유지하도록 하향 이동(Down-Heapify) 합니다.

![](/Data%20Structure/img/ds_heap_priority_queue_2.png)

### 힙의 구조
*인덱스가 1부터 시작하는 것으로 가정

- 자식 노드 구하기(i는 부모 노드의 인덱스)
  - 왼쪽 자식 노드: `2i`
  - 오른쪽 자식 노드: `2i + 1`

- 부모 노드 구하기(i는 자식 노드의 인덱스)
  - 부모 노드: `i / 2`

![](/Data%20Structure/img/ds_heap_priority_queue_3.png)

## 힙의 장단점

### 힙의 장점
| **장점**           | **설명**                                                                                                           |
|--------------------|-------------------------------------------------------------------------------------------------------------------|
| **시간 효율성**    | 힙은 요소 삽입 및 삭제에 대해 평균적으로 O(log n)의 시간 복잡도를 가지며, 배열을 힙으로 변환하는 데는 O(n)의 시간이 소요됩니다. 가장 중요한 점은 최소값 또는 최대값을 O(1) 시간에 가져올 수 있다는 점입니다. |
| **공간 효율성**    | 힙은 완전 이진 트리이므로 배열에 공간 낭비 없이 저장할 수 있습니다.                                           |
| **동적 크기 조정** | 힙은 요소가 삽입되거나 삭제됨에 따라 동적으로 크기를 조정할 수 있어, 실시간 요소 추가/삭제가 필요한 동적 애플리케이션에 적합합니다. |
| **우선순위 기반**  | 힙은 요소를 우선순위에 따라 처리할 수 있어, 실시간 애플리케이션(부하 분산, 의료 애플리케이션, 주식 시장 분석 등)에 적합합니다. |
| **제자리 정렬**    | 힙의 대부분의 응용은 요소를 제자리에서 재배치합니다. 예를 들어, **힙 정렬(HeapSort)**은 추가적인 메모리 사용 없이 요소를 정렬합니다. |

### 힙의 단점
| **단점**           | **설명**                                                                                                       |
|--------------------|-------------------------------------------------------------------------------------------------------------|
| **유연성 부족**    | 힙은 특정 요소의 순서를 유지하도록 설계되었기 때문에, 더 유연한 데이터 구조가 필요한 애플리케이션에는 적합하지 않을 수 있습니다. |
| **검색에 비효율적** | 힙은 최상위 요소에 효율적으로 접근할 수 있지만, 특정 요소를 검색하려면 트리 전체를 순회해야 하며, 이는 O(n)의 시간 복잡도를 가집니다. |
| **안정성 부족**    | 힙은 안정적이지 않은 데이터 구조로, 동일한 값을 가진 요소들의 상대적인 순서가 힙이 구성되거나 수정될 때 보장되지 않습니다.  |
| **복잡성**        | 힙은 삽입, 삭제, 우선순위 큐 구현에는 효율적이지만, 최악의 경우 시간 복잡도가 O(n log n)이 될 수 있어, 더 빠른 알고리즘이 요구되는 애플리케이션에는 최적이 아닐 수 있습니다. |

## 우선순위 큐(Priority Queue)

### 우선순위 큐란?
각 요소가 우선순위를 가지며, 우선순위가 높은 요소가 먼저 처리되는 자료구조입니다. 일반적으로 힙을 사용하여 구현합니다.

## 우선순위 큐의 특징
- 우선순위 기반 처리: 각 요소는 우선순위를 가지며, 우선순위가 높은 요소가 먼저 처리됩니다.
- 동적 크기 조정: 요소의 삽입과 삭제에 따라 크기가 동적으로 변합니다.
- 다양한 구현 방법: 배열, 연결 리스트, 힙(Heap) 등을 사용하여 구현할 수 있습니다.

## 우선순위 큐의 구현 방법
- 배열(Array) 또는 연결 리스트(Linked List):
  - 정렬되지 않은(Unsorted) 경우:
    - 삽입(Insertion): O(1)
    - 삭제(Deletion): O(n) 최대 또는 최소 우선순위 요소를 찾기 위해 전체를 탐색해야 함
  - 정렬된(Sorted) 경우:
    - 삽입(Insertion): O(n) 적절한 위치에 삽입
    - 삭제(Deletion): O(1) 가장 앞이나 뒤에서 제거
- 힙(Heap):
  - 삽입(Insertion): O(log n)
  - 삭제(Deletion): O(log n)
  - 힙을 사용하면 우선순위 큐를 효율적으로 구현할 수 있습니다.

## 참고 자료
1. 면접을 위한 CS 전공지식 노트, 주홍철
2. https://www.geeksforgeeks.org/introduction-to-heap/
3. https://www.geeksforgeeks.org/priority-queue-set-1-introduction/?ref=header_outind
