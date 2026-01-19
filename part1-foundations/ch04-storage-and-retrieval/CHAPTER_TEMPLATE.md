# Chapter 4: Storage and Retrieval

## 핵심 개념

- 로그 구조 저장소 vs 페이지 지향 저장소
- B-Tree와 LSM-Tree
- OLTP vs OLAP 스토리지
- 컬럼 지향 저장소

## 요약

### Storage and Indexing for OLTP
- 해시 인덱스
- SSTable과 LSM-Tree
- B-Tree
- B-Tree vs LSM-Tree 비교
- 기타 인덱스 구조

### Data Storage for Analytics
- 컬럼 지향 스토리지
- 컬럼 압축
- 컬럼 저장소의 쓰기
- Materialized View와 Data Cube

### Query Execution
- 쿼리 실행 계획
- 쿼리 최적화

### Multidimensional and Full-Text Indexes
- 다차원 인덱스 (R-Tree 등)
- 전문 검색 인덱스
- 퍼지 검색

## 중요 용어

| 용어 | 설명 |
|------|------|
| LSM-Tree | Log-Structured Merge Tree |
| SSTable | Sorted String Table |
| B-Tree | 균형 트리 기반 인덱스 |
| Write Amplification | 쓰기 증폭 현상 |
| Compaction | LSM-Tree에서 세그먼트 병합 |
| Column-Oriented | 컬럼 단위로 데이터 저장 |
| Materialized View | 미리 계산된 쿼리 결과 저장 |

## 질문 & 토론

-

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

-
