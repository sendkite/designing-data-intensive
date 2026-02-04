# Chapter 4: Storage and Retrieval

## 핵심 개념

- 로그 구조 저장소(Log-Structured Storage) vs 페이지 지향 저장소(Page-Oriented Storage)
- B-Tree와 LSM-Tree의 차이점 및 trade-off
- OLTP(Online Transaction Processing) vs OLAP(Online Analytical Processing)
- 컬럼 지향 저장소(Column-Oriented Storage)
- 인덱스의 역할과 다양한 인덱스 구조

## 요약

> "컴퓨터는 산술 기계라기보다 파일링 시스템이다." - Richard Feynman

데이터베이스의 핵심 역할:
- **저장(Storage)**: 데이터를 받아서 저장
- **조회(Retrieval)**: 요청 시 데이터를 정확하게 반환

이 장을 통해 특정 저장 엔진에 대한 전문가가 되지는 못하더라도, 선택한 데이터베이스의 문서를 이해하는 데 필요한 충분한 용어와 개념을 습득

### Storage and Indexing for OLTP

#### 기본 개념
- key-value 형태의 저장에서 쓰기는 비교적 단순하지만, 읽기(검색)가 문제
- 빠른 검색을 위해 **인덱스** 구조가 필요

#### 인덱스(Index)
- 기본 데이터에서 파생되는 **추가 구조**
- Trade-off:
  - 읽기 속도 향상
  - 디스크 공간 소비 및 쓰기 속도 저하
- 적절한 인덱스 선택이 중요

#### Log-Structured Storage (저장소)

**1. 메모리 HashMap 방식의 한계**
- 장점: 빠른 접근
- 단점:
  - 디스크 공간이 해제되지 않음
  - 데이터베이스 재시작 시 HashMap 재구축 필요
  - 많은 랜덤 I/O 발생
  - Range 쿼리에 비효율적 (개별 접근만 가능)

**2. Sorted String Table (SSTable)**
- Key가 정렬된 상태로 데이터 저장
- **Sparse Index**: 킬로바이트 단위 블록으로 나누고, 각 블록의 첫 key만 인덱스에 저장
  - 세그먼트로 분리(노트 한장씩 차곡차곡 쓰고 다쓰면 다음 노트 한장한장...)
- 읽기는 빠르나 쓰기가 어려움 (정렬 유지 필요)

**3. LSM-Tree (Log-Structured Merge Tree)**
- append-only-log와 sorted file의 하이브리드 방식
- 1996년 LSM tree 이름으로 연구 발표
- 동작 방식:
  1. 쓰기 → 메모리 내 정렬된 구조(**MemTable**)에 추가 (red-black tree, skip list, trie)
  2. 모든 변경을 append-only log로 MemTable에 기록 (WAL: Write-Ahead Log)
  3. MemTable이 일정 크기 도달 → 백그라운드에서 **Compaction** 수행
  4. SSTable로 순차적으로 **Flush**
- 성능 최적화를 위해 Bloom Filter 포함하는 경우 많음
  - 읽기 요청
    → SSTable L0 확인
    → SSTable L1 확인
    → SSTable L2 확인
    → ... 낭비..!!!!
  - 없으면 검색도 하지 않도록 해주는 역할

#### B-Tree
- 가장 널리 사용되는 인덱스 구조
- 1970년 도입되어어 거의 모든 관계형 데이터베이스에서 표준 인덱스 구현 방식
- 고정 크기의 **페이지(page)** 단위로 데이터 관리
  - 페이지는 파일 바인더 같음(필요한 페이지 찾아서 수정 == in-place로 덮어쓴다)
- 균형 트리 구조 유지 → O(log n) 검색 보장
- 저널링
  - 쓰기의 경우 페이지 덮어쓰기 때문에 위험함
  - 일반적으로 사전 기록 로그 WAL 포함하여 페이지 쓰기 전 기록 로그 남김
  - 데이터베이스 충돌 후 재가동하거나 할때 이 로그로 복원 (저널링(Journaling))

#### B-Tree vs LSM-Tree 비교

| 특성 | B-Tree | LSM-Tree |
|---|---|---|
| 쓰기 성능 | 쓰기 많을수록 불리 | 매우 빠름 (순차 쓰기) |
| 읽기 성능 | Point / Range 모두 강점 | Point lookup은 빠름, Range query는 상대적으로 느림 |
| Write Amplification | 높음 | Compaction 때문에 높아질 수 있음 |
| 공간 효율성 | 파편화 가능 | 압축률 높음 |
| 동시성 제어 | 성숙하지만 복잡 | Write는 단순, Compaction은 복잡 |

- 쓰기 증폭은 낮을 수록 좋음 (낮으면 SSD 마모 줄임)

#### Multi-Column and Secondary Indexes
- 복합 인덱스: 여러 컬럼을 조합한 인덱스
- Secondary Index: 기본 키 외의 컬럼에 대한 인덱스
- 인덱스 
  - 클러스터 인덱스: 인덱스의 리프 노드에 실제 row 데이터가 저장되는 방식
  - 힙 파일: 테이블 데이터는 힙 파일에 저장되고, 인덱스는 해당 row의 위치(TID)를 참조하는 방식 
  - 커버링 인덱스: 쿼리 필요 모든 컬림이 인덱스만 보고 처리 
  - index with included columns: 탐색에 사용하지는 않지만, 조회는 covering 처리

| 항목 | PostgreSQL | MySQL InnoDB |
|------|------------|---------------|
| 테이블 저장 | Heap File | Clustered Index |
| 데이터 정렬 | ❌ | PK 기준 |
| 인덱스 리프 | TID (block_id, offset) | PK 또는 row |
| PK 변경 비용 | 낮음 | 매우 큼 |
| Range Query | 불리 | 유리 |

#### Keeping Everything in Memory
- In-Memory Database: 모든 데이터를 메모리에 유지
  - Memcached
  - Redis
  - Couchbase

### Data Storage for Analytics

- Cloud Data Warehouse
  - Teradata, Vertica, SAP HANA
  - Google Cloud BigQuery
    - Google Dataflow (데이터 처리 프레임워크)
  - Amazon Redshift
    - Amazon Kinesis (데이터 처리 프레임워크)
  - Snowflake

- Open source
  - Apache Hive
  - Trino
  - Apache Spark
  - 요즘 추세는 구성요소 분리 조합
    - Query Engine
    - Storage format
    - Table Format
    - Data Catalog

- Column-Oriented Storage
  - 행(row) 대신 열(column) 단위로 데이터 저장
  - Snowflake, DuckDB, Pinot, Druid . . .
  - 장점:
    - 분석 쿼리에서 필요한 컬럼만 읽음 → I/O 감소
    - 같은 타입 데이터가 연속 → 높은 압축률
  - 압축 기법: Run-length encoding, Bitmap encoding 등

#### Query Execution
- **Compilation**: 쿼리를 기계 코드로 컴파일 후 메모리에 로드된 열 인코딩 데이터에서 실행
- **Vectorization**: 벡터 단위로 데이터 처리 → CPU 캐시 활용 극대화

#### Materialized Views and Data Cubes
- **Materialized View**: 자주 사용되는 쿼리 결과를 미리 계산하여 저장
- **Data Cube (OLAP Cube)**: 다차원 집계 데이터 구조

### Multidimensional and Full-Text Indexes

#### 다차원 인덱스
- **R-Tree**: 공간 데이터 인덱싱 (지리 정보, 좌표 등)
- 범위 쿼리, 최근접 이웃 검색에 유용

#### Full-Text Search (전문 검색)
- 텍스트 내용 기반 검색
- Inverted Index 사용
- 일반테이블 : id를 key로 사용
```shell
문서ID | 내용 
1     | "I love database"
2     | "database index is fast"
```
- inverted index : 단어를 Key로 사용
  - term / postingList 
```shell
"I"        → [1]
"love"     → [1]
"database" → [1, 2]
"index"    → [2]
"fast"     → [2]
```
     
- 퍼지 검색(Fuzzy Search): 오타나 유사 단어 매칭

#### Vector Embeddings
- 벡터 유사도 기반 검색 (ML/AI 활용)
- 의미적 유사성 검색 가능

## 중요 용어

| 용어 | 설명 |
|------|------|
| LSM-Tree | Log-Structured Merge Tree. 쓰기 최적화된 인덱스 구조 |
| SSTable | Sorted String Table. 정렬된 키-값 쌍을 저장하는 파일 형식 |
| MemTable | 메모리 내 정렬된 데이터 구조 (LSM-Tree의 구성 요소) |
| B-Tree | 균형 트리 기반 인덱스. 가장 널리 사용되는 인덱스 구조 |
| Write Amplification | 하나의 쓰기 작업이 여러 번의 물리적 쓰기를 유발하는 현상 |
| Compaction | LSM-Tree에서 여러 SSTable을 병합하고 중복/삭제된 데이터 정리 |
| Column-Oriented | 행 대신 열 단위로 데이터를 저장하는 방식 |
| Materialized View | 미리 계산된 쿼리 결과를 저장한 뷰 |
| WAL | Write-Ahead Log. 내구성을 위해 변경 사항을 먼저 로그에 기록 |
| Sparse Index | 모든 키가 아닌 일부 키만 인덱스에 저장하는 방식 |
| Inverted Index | 단어 → 문서 매핑을 저장하는 전문 검색용 인덱스 |

## 질문 & 토론

1. "우리 서비스 DB는 적절한 선택인가?"
   - 현재 사용 중인 DB의 저장소 구조는?
   - 우리 서비스의 읽기/쓰기 비율과 맞는가?
   - 더 나은 대안이 있을까?

2. "인덱스, 얼마나 만들어야 할까?"
   - 우리 테이블에 인덱스가 몇 개?
   - 안 쓰는 인덱스는 없나?
   - 인덱스 추가 요청이 오면 어떤 기준으로 판단?

3. "새 프로젝트에 PostgreSQL vs MySQL vs DynamoDB?"
   - 각자 선호하는 DB와 이유
   - 저장소 구조 관점에서 근거 제시

4. "DB 느려졌을 때 어디부터 볼까?"
   - 실제 경험 공유

5. 우리가 사용하는 DB의 저장소 방식은 어떨까
   - MySQL
   - Postgres
   - OpenSearch
   - DynamoDB
   - Redis
   

- LSM-Tree가 쓰기에 유리하고 B-Tree가 읽기에 유리한 이유는?
- OLTP 시스템에서 OLAP 쿼리를 실행하면 어떤 문제가 발생하는가?
- 언제 컬럼 지향 저장소를 선택해야 하는가?
- Write Amplification이 SSD 수명에 미치는 영향은?
- 실제 시스템에서 B-Tree와 LSM-Tree를 선택하는 기준은?

## 실습 / 예제

```
# LSM-Tree 동작 흐름

1. Write Request
   ↓
2. MemTable (메모리, 정렬됨)
   ↓ (크기 초과 시)
3. SSTable (디스크, 불변)
   ↓ (Compaction)
4. Merged SSTable

# B-Tree 구조 예시

        [10 | 20]           <- Root
       /    |    \
    [5]   [15]   [25|30]    <- Internal/Leaf
```

```sql
-- OLTP 쿼리 예시 (단일 레코드 조회)
SELECT * FROM orders WHERE order_id = 12345;

-- OLAP 쿼리 예시 (집계)
SELECT
    region,
    product_category,
    SUM(revenue) as total_revenue
FROM sales
WHERE sale_date BETWEEN '2024-01-01' AND '2024-12-31'
GROUP BY region, product_category;
```

## 참고 자료

- Designing Data-Intensive Applications, 2nd Edition - Chapter 4

## 느낀 점

- 세상 존재하는 별의 별 데이터베이스를 살펴보아 좋았다.
- 옵티마이저가 알아서 해주겠지만 default 였는데, 물리적인 저장소 구조에 대해서 생각해 봄 
- 평소 생각 안해본 인덱스 쓰기 방식에 대해 생각해 볼 수 있어 좋았다.
  - 저장소 구조가 결국에 어떻게 조회하냐를 결정하는 것 같다.
  - 저널링 부분 보면서 저장 구조에 대한 아이디어가 프레임워크에서도 보이는 것 같아 재밌었다.
- 앞 장에 나온 star schema라던가 materialization view 용어가 나왔울땨 악슉햐소 뿌듯
- 분석이나 백터 저장소 부분 읽을때 너무 생소하다 보니까 모르는 용어가 많아서 힘들었다 (마지막 백터 부분은 건너 뜀)