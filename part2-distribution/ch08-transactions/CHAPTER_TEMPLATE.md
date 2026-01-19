# Chapter 8: Transactions

## 핵심 개념

- ACID 속성
- 격리 수준
- 직렬성 보장 방법
- 분산 트랜잭션

## 요약

### ACID Properties
- Atomicity: 원자성 (전부 또는 전무)
- Consistency: 일관성 (애플리케이션 불변식)
- Isolation: 격리성 (동시 트랜잭션 격리)
- Durability: 지속성 (커밋된 데이터 영구 저장)

### Isolation Levels
- Read Uncommitted
- Read Committed
  - Dirty Read 방지
  - Dirty Write 방지
- Snapshot Isolation (MVCC)
  - Repeatable Read
  - Write Skew 문제
- Serializable

### Serializability
- 실제 순차 실행
- 2단계 잠금 (2PL)
- 직렬성 스냅샷 격리 (SSI)

### Distributed Transactions
- 2단계 커밋 (2PC)
- 코디네이터 장애 문제
- XA 트랜잭션
- 분산 트랜잭션의 한계

## 중요 용어

| 용어 | 설명 |
|------|------|
| ACID | Atomicity, Consistency, Isolation, Durability |
| Dirty Read | 커밋되지 않은 데이터 읽기 |
| Dirty Write | 커밋되지 않은 데이터 덮어쓰기 |
| MVCC | Multi-Version Concurrency Control |
| Write Skew | 동시 트랜잭션이 다른 데이터 읽고 쓸 때 발생하는 이상 |
| 2PL | Two-Phase Locking |
| 2PC | Two-Phase Commit |
| SSI | Serializable Snapshot Isolation |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
