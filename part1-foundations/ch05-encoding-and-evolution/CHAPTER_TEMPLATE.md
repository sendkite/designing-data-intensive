# Chapter 5: Encoding and Evolution

## 핵심 개념

- 데이터 인코딩 포맷
- 스키마 진화와 호환성
- 데이터 흐름 모드
- 이벤트 기반 아키텍처

## 요약

### Formats for Encoding Data
- 언어별 직렬화 (Java Serialization, Python Pickle 등)
- JSON, XML, CSV
- Thrift, Protocol Buffers
- Avro
- 스키마 진화와 호환성
  - Backward Compatibility
  - Forward Compatibility

### Modes of Dataflow
- 데이터베이스를 통한 데이터 흐름
- 서비스 호출을 통한 데이터 흐름 (REST, RPC)
- 메시지 전달을 통한 데이터 흐름

### Event-Driven Architectures
- 이벤트 기반 시스템의 특징
- 이벤트 브로커
- 비동기 통신의 장단점

## 중요 용어

| 용어 | 설명 |
|------|------|
| Serialization | 인메모리 객체를 바이트 시퀀스로 변환 |
| Encoding | = Serialization |
| Schema Evolution | 스키마 변경에 대응하는 것 |
| Backward Compatibility | 새 코드가 이전 데이터를 읽을 수 있음 |
| Forward Compatibility | 이전 코드가 새 데이터를 읽을 수 있음 |
| Protocol Buffers | Google의 이진 직렬화 포맷 |
| Avro | Apache의 스키마 기반 직렬화 포맷 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
