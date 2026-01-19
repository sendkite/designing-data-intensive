# Chapter 10: Consistency and Consensus

## 핵심 개념

- 일관성 모델
- 선형성 (Linearizability)
- 합의 알고리즘
- 코디네이션 서비스

## 요약

### Linearizability
- 선형성이란? (강한 일관성)
- 선형성이 필요한 경우
  - 잠금과 리더 선출
  - 유일성 보장
  - 크로스채널 타이밍 의존성
- 선형성 구현 방법
- CAP 정리
- 선형성과 네트워크 지연

### Consensus
- 합의 문제 정의
- 합의가 필요한 경우
- FLP 불가능성 정리
- 합의 알고리즘
  - Paxos
  - Raft
  - Zab

### Coordination Services
- ZooKeeper, etcd 같은 서비스
- 제공 기능
  - 선형성 읽기/쓰기
  - 원자적 연산
  - 잠금
  - 장애 감지
- 서비스 디스커버리
- 멤버십 서비스

## 중요 용어

| 용어 | 설명 |
|------|------|
| Linearizability | 모든 연산이 단일 복사본처럼 동작 |
| Sequential Consistency | 연산 순서가 모든 노드에서 동일 |
| Eventual Consistency | 결국에는 일관성 도달 |
| CAP Theorem | Consistency, Availability, Partition Tolerance |
| Consensus | 분산 노드들이 값에 합의하는 것 |
| Paxos/Raft | 대표적인 합의 알고리즘 |
| Leader Election | 리더를 선출하는 과정 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
