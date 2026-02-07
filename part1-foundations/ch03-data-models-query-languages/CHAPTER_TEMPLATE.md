# Chapter 3: Data Models and Query Languages

## 핵심 개념

- 관계형 모델 vs 문서 모델
- 그래프 데이터 모델
- 쿼리 언어의 종류와 특성
- Event Sourcing과 CQRS 패턴

## 요약

데이터 모델은 소프트웨어 개발에서 가장 중요한 부분이다.
> 데이터가 문제에 대한 사고 방식을 결정한다.

애플리케이션은 보통 계층적인 데이터 모델을 가진다.
- (애플리케이션) Object, Data Structure, API 모델링
- (애플리케이션 저장소) JSON, XML, Graph, RDB 테이블
- (데이터베이스 개발자) byte를 메모리, 디스크, 네트워크 영역에서 표현
- (하드웨어 개발자) byte를 전류, 빛의 펄스, 자기장으로 표현

이 장에서는 두번째 계층을 다룬다

RDB Model, Document Model, Graph Model, Event Sourcing, DataFrame을 비교하며 장단점을 살펴본다.

또한, Model을 잘 활용할 수 있는 Query 언어를 살펴본다.

# RDB 모델(Relational Model) versus NoSQL 모델(Document Model)
- 관계형 모델의 탄생과 특징
    - 1970년 Edger Codd의 제안한 SQL로 시작
    - RDBMS 등장으로 1980년 중반 산업 전반에서 선호됨
    - XML, JSON, Graph Data 같은 데이터 유형도 지원하도록 성장
- NoSQL의 등장 배경
    - 2010년대 등장 및 유행 (NoSQL 사용하는 글로벌 기업들의 성공)
    - 데이터를 JSON 형식으로 표현현하여 유연함이 특징
    - MongoDB, Couchbase가 대표적

### (RDB 장점) The Object-Relational Mismatch (Impedance mismatch)

#### Object-Relational Mapping (PRM)
- 객체와 데이터베이스 모델 간 불일치 발생
- 변환 계층이 필요함
- ORM을 사용하면 상용구 코드 양을 줄여주시만 문제점들 있음
    - ORM을 사용한다 해도 여전히 관계형, 객체 모두 고려해야함
    - ORM은 OLTP 기능에만 사용되며 분석은 SQL 쓰기 때문에 여전히 RDB 스키마 설계가 중요함
    - N+1 문제 같은 의도하지 않은 쿼리 작성하기 쉬움
- ORM은 RDB에서만 작동, 타 데이터베이스는 ORM 지원 부족함
- ORM은 캐싱으로 부하 줄이는데 도움, 반복적인 코드 양을 줄려주는 장점도 있음

#### (NoSQL 장점) The document data model for one-to-many relationships

- 모든 데이터가 관계형 데이터에 적합하지는 않음
- 같은 정보라도 JSON 문서 형태로 표현하면 더 자연스럽고 Object structure와 잘 매칭됨
- Document 모델은 Data locality(데이터 지역성)이 좋다
    - JOIN, 인덱싱 같은 과정 없이 한번에 데이터 제공

#### Normalization, Denormalization, and Joins

읽기/쓰기 성능과 구현 노력 측면에서 장단점이 있음

- 정규화
    - 일괄 업데이트, 로컬라이제이션, 국제화, 더 나은 검색 지원
    - 쓰기 속도가 빠르고 읽기 속도가 느림
- 비정규화
    - Document 데이터베이스는 Join 기능이 보통 미흡하여 정규화가 불편함
    - 애플리케이션 단에서 직접 join 수행, MongoDB에서는 집계 파이프라인 기능 지원
    - 읽기 속도가 빠르고 쓰기 비용이 높음
    - 디스크 비용더 많이 사용 (중복 데이터 보유)
    - 구현 노력이 더 필요함
        - 데이터적 일관성, 원자적 트랜잭션이 지원하지 않을 수 있음 (스트림을 통해 일관성 보장해야하는데 12장에 다룸)

#### Many-to-One and Many-to-Many Relationships

- JSON 문서에는 Many To One, Many To Many가 쉽게 담기지 않는다.
- 정규화된 조인 테이블이 더 적합
- 양방향 관계를 맺어야할 수도 있음

#### Stars and Snowflakes: 분석을 위한 Schema

- 데이터 웨어하우스는 일반적으로 관계형
- 널리 사용되는 컨벤션
    - star schema
        - fact_table 중심으로 연결 구조 가지는 것
        - 쿼리 단순, 중복데이터 많음
    - snowflake schema
        - dimension이 다시 dimension을 가지는 구조
        - 쿼리 복잡, 중복 감소로 정합성 높음
    - dimensional modeling
    - one big table (OBT)**
- stars, snowflakes schema는 주로 One To Many 관계

#### 언제 어떤 Model을 사용해야할까?

- Document Model의 장점
    - 스키마 유연성
        - 읽기 스키마만 지원하여 RDB 모델 대비 유연함 (Schema on read)
        - 제품 같이 정해지지 않은 데이터 구조에 적합
    - 데이터 지역성으로 인한 성능 향상
        - store locality : 필요한 데이터가 함께 저장되어 있어 파편화, 인덱싱, 조인 없이 탐색 가능
        - 문서 크기는 항상 작게 유지하고 소규모 업데이트는 피하는 것이 좋음
    - Query 언어 for document Model
        - Document DB에 따라 Query 언어가 제각각
            - Key-Value 접근 허용 only
            - 내부 값 쿼리를 위한 보조 인텍스
            - 어떤 DB는 풍부한 쿼리 언어 제공 등
    - 객체와 모델 유사성
- Document Model의 단점
    - 데이터가 One To Many 형태면 일반적으로 문서 모델을 사용하는 것이 좋다
    - Document 모델은 중첩 항목을 직접 참조하지 못한다
- RDB 모델의 장점
    - 조인
    - ManyToOne, ManyToMany 지원


#### 현대에는 RDB와 Document DB 기능들이 융합되고 있는 추세
- RDB JSON 데이터 지원
- MongoDB, Couchbase 조인, 보조 인덱스 및 선언적 쿼리 언어 지원


### Graph-Like Data Models

- Many To Many가 빈번하다면 데이터 관계의 복잡도가 커짐
- 그래프로 모델링하면 자연스럽게 다룰 수 있음
- 데이터 구조 변경사항 수용이 쉬워 진화성이 좋음

- 그래프는 2개 종류의 Objects로 구성 (점과 선으로 데이터 모델링하는 방식)
    - Vertices (정점: nodes 또는 entities라고도 함)
    - Edges (간선: relationships or arcs라고도 함)


### RDF data Model, SPARQL query 언어, Datalog, Cypher

- Resource Description Framework (RDF)

### GraphQL

- 클라이언트가 사용자 인터페이스 렌더링에 필요한 필드를 포함하는 특정 구조 JSON을 요청할 수 있는 쿼리 언어
- REST, gRPC 사용 서비스에 요청하여 반환
- 권하 부터, 속도 제한 및 성능 문제 같은 추가 고려 필요
- 실행 비용이 많이 드는 작업은 허용하지 않음
- 재귀 쿼리를 허용하지 않음

### Event Sourcing and CQRS
- 복잡한 애플리케이션에서는 데이터를 한 가지 형태로 작성 후 다양한 읽기 방식에 최적화된 표현 방식을 파생시키는 것이 좋을 수 있음
- 쓰기에만 최적화한 데이터 방식은 무엇인가
    - 이벤트 로그 형태의 데이터가 될 것이다
    - 데이터 기록할때마다 자체 timestamp 포함한 자체 JSON 문자열로 인코딩 한 후에 이벤트 적재
    - 로그의 이벤트는 변경 불가능하며 새로운 이벤트 추가하는 것만 가능
    - 이벤트는 임의의 속성을 포함할 수 있음
- 대상의 모든 상태 변경 사항을 이벤트로 저장
    - 이벤트가 로그에 추가될 때마다 materialized views(프로젝션 또는 read model) 이벤트 영향 반영 업데이트

> 이벤트를 source of truth로 사용하고, 모든 state 변경을 event로 표현하는 아이디어를 Event Sourcing이라고 한다

> 읽기 최적화 표현을 별도로 유지하고, 쓰기 최적화 표현에서 파생시키는 원칙을 CQR(Command Query Responsibility Segregation)라고 한다

- Event Sourcing, CQRS 같은 용어는 DDD 커뮤니티에서 유래되었으나 유사 아이디어는 오래전 부터 존재했다. 예를 들면 State Machine의 Using Shared Logs가 있다.

- Event Sourcing Evnet 모델링 할때는 이벤트 이름을 과거형으로 지정하는 것이 좋다
- Event는 순서가 중요하다

#### 이벤트 소싱, CQRS의 장단점

- 장점
    - 이벤트는 개발자에게 어떤 일이 왜 발생했는지 더 명시적으로 잘 전달 됨
        - booking 테이블 4001행 active 열이 false로 변경 되고 seat_assginments 삭제됨 vs 예약이 취소 되었다는 이벤트
    - materialized views는 Event logs 즉 Source에서 파생 되었다는 것이 핵심
        - 데이터를 삭제하고 이벤트를 동일 순서로 처리하면 다시 계산 될수 있어여함
        - 원하는 만큼 반복 실행해서 동작 검사할 수 있어 버그 찾기가 쉽고 유지보수가 쉬워짐
    - 필요에 따라 이벤트 저장소와 다른 데이터 저장소에 파생 데이터를 만들 수 있음
    - 이벤트에 새로운 동작을 연결하는 것이 쉬워서 진화성 갖춤
        - 이벤트가 잘못 기록되거나 하면 후속 삭제 이벤트를 기록하여 되돌릴 수도 있음
    - 이벤트 로그는 audit log의 역할도 수행할 수 있음
    - 이벤트는 순차접근 패턴 덕분에 일반적으로 데이터베이스 보다 높은 쓰기 처리량을 처리 가능
- 단점
    - 외부 정보가 포함된 경우 주의을 요함
        - 외부 호출 데이터가 시간대별로 응답이 다르다면 문제가 될 수 있음 이런 경우 해당 데이터를 이벤트에 포함해야함
    - 이벤트는 삭제가 불가해 이벤트에 개인정보가 포함된 경우, 보안에 문제가 됨
        - 개인 데이터를 이벤트 외부에 저장하거나 암호화 방법도 있으나, 파생 상태 다시 계산할때 더 어렵게 만듬
    - 이벤트 재처리 할때 사이드 이펙트 가능성
        - 예를 들면 이메일 발송 기능이 이벤트에 끼어 있다면 이메일이 여러번 발송되거나 할 수 있음

### DataFrames, Matrices, and Arrays
- 과학/분석 데이터를 위한 데이터 모델
- 다차원 배열 처리
- DataFrame은 R언어, 파이썬의 Pandas 라이브러리, Apache Spark 등에서 지원하는 데이터 모델
    - 머신러닝 모델 학습 뿐만 아니라, 데이터 탐색, 통계 분석, 데이터 시각화 등 폭넓게 사용됨

### 기타 모델

- 금융 시스템 ledgers(원장) 복식 부기 모델(double-entry accounting)
- 유전자 게놈 데이터의 서열 유사성 검색 모델
- Full-text search 전문 검색 데이터 모델

## 중요 용어

| 용어              | 설명                                                      |
|-----------------|---------------------------------------------------------|
| Schema-on-Read  | 읽을 때 스키마 해석 (문서 DB)                                     |
| Schema-on-Write | 쓸 때 스키마 검증 (관계형 DB)                                     |
| star schema     | fact 테이블을 두고 관련 데이터 참조 키를 가지게 하는 모델링 기법                 |
| snowflake schema | star schema와 달리 dimension에 하위 dimension을 연결하여 모델링 하는 기법 |
| Graph Model     | 데이터를 노드와 엣지로 구분하는 모델링                                   |
| Event Sourcing  | 변경을 이벤트를 로그로 저장하고 해당 이벤트로 파생 구현 패턴                      |
| CQRS            | 읽기와 쓰기 모델을 분리하는 패턴                                      |


## 질문 & 토론

- 새 프로젝트에서 RDB vs Document DB를 어떻게 선택해야할까?
    - One To Many면 Document, Many To Many면 RDB, Many To Many가 너무 많으면 Graph라고 했는데
        - 실제로는 관계가 어떻게 진화할지 예측할 수 없지 않을까?
        - Document DB로 Many To Many 어떻게 풀어햐할까? 데이테 정합성, 중복 관리는?

- Graph Model이 적합한 사례가 어디있을까?
    - SNS 예제가 있긴한데 RDB로도 괜찮지 않나 싶음

- stars schema, snowflake schema에 대한 의견이 궁금, 우리 시스템에 도입하면 어떤게 방식으로 접근할까?

- Event는 영원하다. 이벤트의 변경 관리 best practice는?

## 실습 / 예제

-

## 참고 자료

-

## 느낀 점

- 책 원제가 Designing Data Intensive Application인 만큼, 시스템의 종류에 따라 다양한 Data Modeling 기법을 볼 수 있어 좋았다.
    - 특히 데이터 분석쪽 DataFrames나 Graph Model 쪽은 너무 생소해서 모르는 분야를 알게 되서 좋았다.
    - 너무 생소하다 보니 GPT 도움을 받아봤는데, 읽는데 시간이 걸려서 skip 했다 (당장 사용 할 수 없는 지식이라 판단)
- 책에서 Event Sourcing, CQRS도 Data Modeling으로 분류했는데, 아키텍처 접근 보다 친근하고 잘 이해되는 것 같았다.
    - 그 동안 내가 잘못된 개념으로 알고 있었구나 싶었음
- Entity, Domain, DB Data 모델링을 벗어나 더 큰 관점에서 Data Modeling 디자인에 대해 생각 할 수 있어 좋았다
