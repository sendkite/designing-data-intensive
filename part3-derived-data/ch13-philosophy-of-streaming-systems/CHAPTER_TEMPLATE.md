# Chapter 13: A Philosophy of Streaming Systems

## 핵심 개념

- 데이터 통합의 중요성
- 데이터플로우 중심 애플리케이션 설계
- 정확성과 무결성

## 요약

### Data Integration
- 데이터 통합의 필요성
- 파생 데이터와 원본 데이터
- 변경 데이터 캡처 (CDC)
- 이벤트 소싱 revisited
- 로그 컴팩션

### Designing Applications Around Dataflow
- 데이터베이스를 뒤집어 생각하기
- 읽기 경로와 쓰기 경로
- 파생 상태와 원본 상태 분리
- 스트림 프로세서와 서비스
- 애플리케이션 상태 관찰

### Correctness and Integrity
- 데이터 시스템의 정확성
- 엔드-투-엔드 정확성
- 제약 조건 강제
- 적시성과 무결성
- 감사와 추적성

## 중요 용어

| 용어 | 설명 |
|------|------|
| CDC | Change Data Capture, 변경 데이터 캡처 |
| Log Compaction | 로그에서 키당 최신 값만 유지 |
| Derived Data | 다른 데이터에서 파생된 데이터 |
| Unbundling | 데이터베이스 기능을 분리하는 것 |
| End-to-End Argument | 기능은 끝점에서 구현해야 함 |
| Audit Trail | 변경 이력 추적 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
