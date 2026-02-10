# Chapter 6: Replication

## 핵심 개념

- 복제의 목적: 고가용성, latency 감소, 읽기 확장
- 리더 기반 복제 
- 복제 지연 문제 (Replication Lag)
- 충돌 해결
- Eventual-consistency, Read-your-write, Monotonic reads

## 요약

> Replication이란 네트워크를 통해 동일한 데이터를 여러대 컴퓨터에 저장하는 것

다음과 같은 이유로 Replication을 한다.
- 데이터를 사용자와 지리적으로 가까운 곳에 보관하여 지연 시간 줄이기
- 시스템 일부가 다운 되도 동작할 수 있게 고가용성과 내구성을 목적으로
- 읽기 쿼리 처리할 수 있는 머신을 늘려 읽기 처리량을 늘리기 위해

### Single-Leader Replication
- Replica: database의 복제본

> Replica가 여러대라면 모든 데이터가 모든 복제본에 저장되도록 어떻게 보장할 수 있을끼?

#### 리더 기반 복제 (Leader-based replication, Primary-backup, Active/Passive)
- Replicas 중에 1대가 Leader(=Primary, Source)로 지정
- 클라이언트가 데이터베이스에 데이터를 쓰면, 리더가 먼저 local storage에 write
- 다른 Replicas는 followers(=Read Replica, secondaries, hot standbys)라고 하며 local storage에 데이터가 기록되면, replication log 또는 change stream의 일부로 데이터 변경 사항을 받아 데이터 업데이트
- followers는 읽기 전용으로 사용되며 쓰기는 Leader만 허용 
- 데이터베이스가 샤딩 되었다면 서로 다른 샤드가 Leader 역할 수행
- MySQL, PostgresSQL 등 단일 노드 복제 기능 내장하고 있음
- Kafka, DynamoDB 같은 곳에서도 사용중

#### 동기식 vs 비동기식 복제

> Leader와 Follower의 데이터 동기화 지연이 생길 수 있다. 어떻게 동기화 할 것인가

- semi-synchronous (준동기식)
  - followers 1대는 동기식, followers 나머지는 비동기식인 경우가 많음
  - 동기식 followers가 죽으면 다른 followers가 동기식으로 격상
- Quorums (과반수 동기식)
  - replica 5대중 3대가 동기적 업데이트하는 방식
- 전부 비동기식 
  - Leader에 장애가 발생하거나 해서, 모든 followers에 지연이 있더라도 비동기로 운영
  - 특히 followers가 많거나, replica가 지리적으로 분산된 경우 널리 사용됨

#### Setting Up New Followers

> 다운타임 없이 followers를 어떻게 추가할 수 있을까?

- Leader에 일관된 스냅샷을 특정 시점에 생성
- 스냅샷을 새 Followers 노드에 복제
- Followers는 Leader애 연결하여 스냅샷이 생성된 이후 발생 데이터 변경사항 요청
- Leader에 복제 로그 (MySQL - binlog coordinates/GTIDs, PostgresSQL - log sequence number)에서 변경사항 읽음
- Followers가 변경사항 누적 처리 완료하면 실시간으로 데이터 변경사항 처리

> Tips : 데이터 복제에 S3 같은 Object Storage를 활용하면 비용 절약 및 데이터 통합 간소화 가능

> Object Storage 단점으로 지연시간이 길다, 요즘은 계층형 스토리지 아키텍텍처 구성하여 지연 없는 메모리 storage를 섞어 사용함 (a tiered storage architecture)


#### 노드 장애 처리

> 리더, 팔로우 down 되면 어떻게 할까? 

  - 팔로워 장애: Catch-up Recovery
    - 팔로워는 리더 수신 데이터 변경 log를 저장
    - 다운되고 재시작되면 log 기반으로 쉽게 복구가 가능
    - 그러나 Leader 데이터 처리향이 너무 많거나, 팔로워가 너무 오랫동안 죽었다면 리더에 부하가 발생할 수 있다.
  - 리더 장애: Failover
    - 다음 과정을 failover라고 한다
      - Leader가 죽음 -> followers중 하나가 Leader로 승격 -> followers들이 새 leader를 바라보록 재구성 
    - Leader가 죽은 것은 time-out으로 감지
      - 적절한 타임아웃은 언제일까? 너무 짧으면 불필요한 failover 발생
    - Failover에는 많은 사이드이펙트 가능성 있음
      - Redis 동기화 문제
      - 데이터 유실, 손상 문제
      - 서로 Leader가 되려는 경합 (Split brain)
    - 가장 중요한 것은 가장 최신 상태의 팔로워를 Leader로 선택하는 것

> 노드 장애, 불안정한 네트워크, 복제본 일관성, 내구성, 가용성 및 지연 시간 등은 시스템의 근본적인 문제

#### 복제 로그 구현 방식

- Statement-based replication
- Write-ahead log (WAL) Shipping
- Logical(row-based) log replication


### Replication Lag (복제 지연 문제)

> Leader와 Followers는 최종 일관성(eventual consistency)으로 동작한다.

- 복제 지연은 상황에 따라 몇 초 또는 몇 분까지 늘어날 수 있다.
- 이런 문제 해결 접근 방식

#### Reading Your Own Writes

#### Monotonic Reads

#### Consistent Prefix Reads

#### Solutions for Replication Lag

### Multi-Leader Replication

## 중요 용어

| 용어 | 설명 |
|------|------|
| Leader/Primary | 쓰기를 받는 주 노드 |
| Follower/Replica | 리더의 복제본을 유지하는 노드 |
| Synchronous Replication | 팔로워 확인 후 응답 |
| Asynchronous Replication | 팔로워 확인 없이 응답 |
| Failover | 리더 장애 시 새 리더 선출 |
| Quorum | 읽기/쓰기에 필요한 최소 노드 수 |
| Split Brain | 두 노드가 모두 자신을 리더로 인식 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
