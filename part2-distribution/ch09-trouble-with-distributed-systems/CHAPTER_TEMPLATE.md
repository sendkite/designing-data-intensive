# Chapter 9: The Trouble with Distributed Systems

## 핵심 개념

- 분산 시스템의 본질적 어려움
- 부분 장애 (Partial Failure)
- 신뢰할 수 없는 네트워크와 시계
- 진실과 거짓말

## 요약

### Faults and Partial Failures
- 단일 컴퓨터 vs 분산 시스템
- 부분 장애의 비결정성
- 클라우드 vs HPC 접근 방식

### Unreliable Networks
- 네트워크 장애 유형
- 타임아웃과 무한 대기
- 네트워크 혼잡과 큐잉
- 동기식 vs 비동기식 네트워크

### Unreliable Clocks
- 시간 측정의 어려움
- Time-of-Day Clock vs Monotonic Clock
- 클럭 동기화와 정확도
- 이벤트 순서에 의존하는 것의 위험
- 프로세스 중단 (GC, 페이지 폴트 등)

### Byzantine Faults
- 비잔틴 장애란?
- 약한 형태의 거짓말 (하드웨어 오류 등)
- 비잔틴 장애 허용이 필요한 경우

## 중요 용어

| 용어 | 설명 |
|------|------|
| Partial Failure | 시스템 일부만 실패하는 상황 |
| Network Partition | 네트워크 분리로 노드 간 통신 불가 |
| Timeout | 응답 대기 시간 제한 |
| Clock Drift | 시계가 실제 시간과 벌어지는 현상 |
| NTP | Network Time Protocol |
| Byzantine Fault | 노드가 잘못된 정보를 보내는 장애 |
| Fencing Token | 오래된 요청을 거부하기 위한 토큰 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
