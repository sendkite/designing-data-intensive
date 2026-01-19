# Chapter 11: Batch Processing

## 핵심 개념

- 배치 처리 vs 서비스 (온라인 시스템)
- Unix 철학과 데이터 처리
- MapReduce 패러다임
- 데이터플로우 엔진

## 요약

### Batch Processing with Unix Tools
- Unix 철학
- 파이프라인 조합의 힘
- 단순한 도구들의 조합 (awk, sort, uniq)
- 로그 분석 예제

### MapReduce
- 분산 파일 시스템 (HDFS)
- MapReduce 작업 흐름
  - Map: 레코드에서 키-값 추출
  - Shuffle: 키별로 그룹화
  - Reduce: 집계
- MapReduce의 장점과 한계
- Reduce-Side Join vs Map-Side Join
- 출력의 활용

### Dataflow Engines
- Spark, Flink, Tez
- MapReduce의 문제점 극복
- 연산자 그래프
- 내결함성 (체크포인팅, 재계산)

### Batch Use Cases
- 검색 인덱스 구축
- 머신러닝
- 키-값 저장소 구축

## 중요 용어

| 용어 | 설명 |
|------|------|
| Batch Processing | 대량 데이터를 한번에 처리 |
| HDFS | Hadoop Distributed File System |
| MapReduce | Map과 Reduce 두 단계로 분산 처리 |
| Shuffle | Map 출력을 Reduce로 전달하는 과정 |
| DAG | Directed Acyclic Graph (데이터플로우) |
| Materialization | 중간 결과를 저장하는 것 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
