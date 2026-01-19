# Chapter 7: Sharding

## 핵심 개념

- 파티셔닝(샤딩)의 목적
- 파티션 전략
- 파티션과 보조 인덱스
- 리밸런싱

## 요약

### Sharding Strategies
- 키 범위 기반 파티셔닝 (Range Partitioning)
  - 순차 읽기에 유리
  - 핫스팟 위험
- 해시 기반 파티셔닝 (Hash Partitioning)
  - 균등 분산
  - 범위 쿼리 어려움
- 복합 키 전략
- 핫스팟 완화 방법

### Request Routing
- 클라이언트가 파티션 찾는 방법
- 파티션 라우팅 방식
  - 아무 노드에 요청
  - 라우팅 티어 사용
  - 클라이언트가 파티션 인식
- 코디네이션 서비스 (ZooKeeper)

### Sharding and Secondary Indexes
- 문서 기반 파티셔닝 (Local Index)
- 용어 기반 파티셔닝 (Global Index)
- Scatter/Gather 쿼리

## 중요 용어

| 용어 | 설명 |
|------|------|
| Shard/Partition | 데이터의 부분 집합 |
| Consistent Hashing | 노드 추가/제거 시 최소 재배치 |
| Hot Spot | 특정 파티션에 부하 집중 |
| Rebalancing | 파티션 재배치 |
| Local Index | 파티션 내 보조 인덱스 |
| Global Index | 전체 데이터에 대한 보조 인덱스 |
| Scatter/Gather | 여러 파티션에 쿼리 후 결과 병합 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
