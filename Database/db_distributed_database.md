# 분산형 데이터베이스

- 데이터를 한 위치가 아닌 여러 위치에 저장하는 데이터베이스
- 이는 여러 서버 또는 개별 노드로 구성된 컴퓨터 클러스터에 데이터를 배치하는 것을 의미한다. 이러한 노드는 지리적으로 분리되어 있을 수 있으며, 클라우드 데이터베이스 내의 물리적 컴퓨터 또는 가상 머신일 수 있다.

# 유형

- **동종 분산 데이터베이스** : **동일한** 데이터를 저장, 데이터 모델 사용, 운영 체제에서 작동, 분산 데이터 베이스 관리 시스템 공유. 모든 노드의 유사성으로 인해 중복성을 통해 중요한 데이터 보호를 제공하고 관리를 단순화한다.

- **이기종 분산 데이터베이스** : **서로 다른** 데이터를 저장, 운영 체제에서 작동, 데이터 스키마를 포함한다. 혹은, 서로 다른 사이트가 서로의 존재를 인식하지 못할 수 도 있다. 관리하기가 더 복잡하지만 이기종 분산 데이터베이스는 동종 분산 데이터베이스보다 데이터 모델, 스키마 선택 및 저장할 수 있는 데이터 유형 측면에서 더 큰 유연성을 제공한다.

# 주로 사용되는 곳

- **대규모 데이터와 트래픽을 처리해야 하는 애플리케이션** : 소셜 미디어 플랫폼, 전자 상거래 사이트 등 수백만 명의 동시 사용자 및 트랜잭션을 관리해야 하는 경우. 분산 데이터베이스는 많은 노드를 추가하여 수평적으로 확장할 수 있도록 지원한다.

- **높은 가용성과 장애 허용이 필수적인 환경** : 금융 서비스, 의료 시스템, 결제 처리 시스템 등 거의 지속적인 가동 시간이 필요한 경우.

# 노드

노드는 분산 데이터베이스 시스템 내에 있는 개별 서버 또는 컴퓨터다(예: 물리적 구성 요소를 공유하지 않는 컴퓨터, 가상 머신, 서버).

각 노드는 데이터를 저장하고 분산 데이터베이스 관리 시스템 소프트웨어에서 실행된다.

# 결함 허용 (Fault Tolerance)

하나 이상의 노드 혹은 서버의 실패에도 계속해서 정상적으로 동작할 수 있는 능력을 의미한다. 시스템의 안정성과 가용성을 보장하며, 장애가 발생했을 때에도 서비스의 중단을 최소화하는 목표를 가진다.

## 데이터 복제

서로 다른 노드 혹은 서버, 사이트에 동일한 데이터의 복사본을 만들고 유지하는 프로세스이다. 이렇게 되면, 하나의 노드 혹은 서버에서 장애가 발생하더라도, 다른 쪽에서 동일한 데이터를 제공할 수 있어 가용성이 증가한다.

- **전체 복제:** 전체 복제에서 전체 데이터베이스의 완전한 복사본이 분산 데이터베이스 시스템 내의 모든 사이트로 전송된다.
- **부분 복제:** 데이터베이스의 특정 부분만 필요한 경우가 있으므로 데이터베이스의 정의된 부분이 선택한 그룹에 복제된다.

## 데이터 분산(파티셔닝)

파티셔닝은 대규모 데이터 세트를 더 작고 관리하기 쉬운 부분으로 분할하는 기술로써, 분산 데이터베이스에서 효율성, 보안 및 최적의 사용자 액세스에 매우 중요하다.

- **수평 파티셔닝**

  - 데이터 테이블을 행으로 분할하는 것을 의미.
  - 전체 데이터 세트를 스캔하는 대신 특정 파티션으로 쿼리를 보낼 수 있으므로 효율적인 부하 분산과 향상된 쿼리 성능을 가능하게 한다.

- **수직 파티셔닝**

  - 데이터 테이블을 열로 분할한다.
  - 서로 다른 애플리케이션이나 사용자가 데이터의 서로 다른 속성에 접근할 때 특히 유용하다. 자주 접근하는 열을 격리함으로써 수직 파티셔닝은 성능을 향상시키고 네트워크를 통해 전송되는 데이터 양을 줄일 수 있다.

- **해시 파티셔닝**
  - 지정된 키 속성에 해시 함수를 적용하여 주어진 레코드를 보유할 파티션을 결정한다.
  - 파티션 간에 데이터를 고르게 분산시켜 하나의 파티션이 불균형적으로 많은 트래픽을 수신하는 가능성을 최소화하는 것을 목표로 한다.

## 부하 분산

사용자 요청 및 쿼리를 데이터베이스 노드에 고르게 분산하는 기술. 이는 성능을 향상시킬 뿐만 아니라 한 노드의 오류가 다른 노드에 과부하를 일으키지 않도록 한다.

쿼리가 수신되면 부하 분산 장치는 요청을 평가하고 응답하는 데 가장 적합한 노드를 결정한다. 이 평가 중에 근접성, 현재 부하 및 기타 미리 결정된 시스템 규칙과 같은 요소가 고려된다. 이 평가 및 할당은 시스템 과부하 및 시스템 비효율성을 방지하여 사용자 대기 시간을 줄이는 데 도움이 된다.
