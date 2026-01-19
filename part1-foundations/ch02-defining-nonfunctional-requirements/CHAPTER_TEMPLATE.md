# Chapter 2: Defining Nonfunctional Requirements

## 핵심 개념

- 성능 측정 방법 (지연 시간, 처리량)
- 신뢰성과 장애 허용
- 확장성 패턴
- 유지보수성

## 요약

### Case Study: Social Network Home Timelines
- 트위터 타임라인 구현 사례
- Fan-out 문제와 해결책
- 읽기 최적화 vs 쓰기 최적화 트레이드오프

### Describing Performance
- Latency vs Response Time
- 백분위수(Percentile): p50, p95, p99
- Throughput 측정

### Reliability and Fault Tolerance
- Fault vs Failure
- 하드웨어 장애
- 소프트웨어 오류
- 인적 오류

### Scalability
- 부하 기술하기 (Load Parameters)
- 수직 확장(Scale Up) vs 수평 확장(Scale Out)
- Elastic vs Manual Scaling

### Maintainability
- Operability: 운영 용이성
- Simplicity: 복잡성 관리
- Evolvability: 변경 용이성

## 중요 용어

| 용어 | 설명 |
|------|------|
| Latency | 요청이 처리되기를 기다리는 시간 |
| Response Time | 클라이언트 관점에서 요청~응답 전체 시간 |
| Throughput | 단위 시간당 처리량 |
| Percentile | 백분위수 (p99 = 99%의 요청이 이 시간 이내) |
| Fan-out | 하나의 요청이 여러 서비스로 분산되는 것 |
| SLA/SLO | Service Level Agreement/Objective |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
