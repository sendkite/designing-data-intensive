# Chapter 12: Stream Processing

## 핵심 개념

- 이벤트 스트림
- 메시지 브로커
- 스트림 처리 패턴
- 스트림 처리의 내결함성

## 요약

### Messaging Systems
- 메시지 전달 보장
  - At-most-once
  - At-least-once
  - Exactly-once
- 직접 메시징 vs 메시지 브로커
- 로그 기반 메시지 브로커 (Kafka)
- 파티셔닝과 소비자 그룹

### Processing Streams
- 스트림 처리 용도
- 이벤트 시간 vs 처리 시간
- 윈도우 연산
  - Tumbling Window
  - Hopping Window
  - Sliding Window
  - Session Window
- 스트림 조인
  - Stream-Stream Join
  - Stream-Table Join
  - Table-Table Join

### Fault Tolerance
- 마이크로배칭 (Spark Streaming)
- 체크포인팅
- 원자적 커밋
- 멱등성 (Idempotence)

## 중요 용어

| 용어 | 설명 |
|------|------|
| Event Stream | 시간순 이벤트의 연속 |
| Message Broker | 메시지 중개 시스템 |
| Consumer Group | 파티션을 나눠 소비하는 소비자 그룹 |
| Offset | 로그 내 메시지 위치 |
| Event Time | 이벤트 발생 시간 |
| Processing Time | 이벤트 처리 시간 |
| Watermark | 이벤트 시간 진행 추적 |
| Exactly-Once | 정확히 한 번 처리 보장 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
