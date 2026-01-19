# Chapter 6: Replication

## 핵심 개념

- 복제의 목적: 고가용성, 지연 시간 감소, 읽기 확장
- 리더 기반 복제
- 복제 지연 문제
- 충돌 해결

## 요약

### Single-Leader Replication
- 동기식 vs 비동기식 복제
- 새로운 팔로워 설정
- 노드 장애 처리
  - 팔로워 장애: Catch-up Recovery
  - 리더 장애: Failover
- 복제 로그 구현 방식

### Multi-Leader Replication
- 사용 사례: 다중 데이터센터, 오프라인 클라이언트
- 쓰기 충돌 처리
- 충돌 회피
- 충돌 해결 전략
- 복제 토폴로지

### Leaderless Replication
- Dynamo 스타일 시스템
- 쿼럼 읽기/쓰기 (w + r > n)
- 동시 쓰기 감지
- 버전 벡터

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
