# Chapter 5: Encoding and Evolution

## 핵심 개념

- 데이터 인코딩 포맷
- 스키마 진화와 호환성
- 데이터 흐름 모드
- 이벤트 기반 아키텍처

## 요약

진화 가능성
- 스키마, 데이터 형식 변화 수반
- 고객이 버전별 업데이트 선택 가능 형태 일 수 있음
- 롤링 배포로 빈번한 릴리즈
- 양방향의 호환성 유지 필요
  - Backward compatibility(하위 호환성)
    - 구버전 내용 신버전에 호환
  - Forward compatibility(전방 호환성)
    - 구버전은 최신 코드 내용 무시

## Formats for Encoding Data
- 메모리에서 데이터는 객체, 구조체, 리스트, 배열, 해쉬 테이블, 트리 등의 형태로 저장된다.
- 파일에 데이터를 저장하거나, 네트워크로 전송할때, 어떤 형태의 독립적인 바이트 시퀀스(ex. JSON)로 인코딩해야 한다.
- 따라서 메모리/파일/네트워크 간 데이터 변환이 필요하다.
  - 메모리내 표현을 바이트 시퀀스로 변환하는 과정을 encoding(또는 직렬화, 마샬링)
  - 바이트 시퀀스를 메모리내 표현으로 변환하는 과정을 decoding(또는 파싱, 역직렬화, 언마샬링)이라 한다.

### Language Specific formats (언어별 형식)

- 언어별 인코딩 기능 라이브러리 기본 지원
  - Java : java.io.Serializable
  - Python : pickle
  - Ruby : Marshal
- 그러나 이런 라이브러리는 근본적인 문제점을 가진다.
  - 프로그래밍 언어 종속되어 다른 조직, 시스템 통합 불가능

### JSON, XML, Binary Variants

- 프로그래밍 언어 관련 없는 표준화된 인코딩 후보 JSON, XML
- 역시 문제점
  - XML : 장황하고, 숫자, 문자열 구분 하지 못함
  - JSON : 부동 소수점, 정수 구분하지 못함
  - 바이너리 문자열 지원하지 않음, 필요시 Base64로 인코딩은 하나, 데이터 크기 33% 증가

#### JSON Schema

- 시스템 간 데이터 교환이나 저장소 기록시 데이터 모델링 방식으로 널리 채택
  - Web Service Open API
  - Confluent Schema registry
  - PostgresSQL pg_jsonschema, MongoDB $jsonSchema
- 개방형/폐쇄형 content model 지원
  - 개방형 default (additionalProperties: true)
    - 스키마에서 정의되지 않은 모든 필드 허용
  - 폐쇄형
    - Schema 정의된 필드만 허용
- 유효성 검사기 지원

### Binary encoding

- JSON이나 XML은 여전히 많은 공간을 차지
- 이진 인코딩을 활용하면 더 간결하고 파싱 속도를 높일 수 있음

- 이진 인코딩 형식들 
  - JSON (MessagePack, CBOR, BSON, BJSON, UBJSON, BISON, Hessian, and Smile 등) 
  - XML (WBXML and Fast Infoset).

#### Protocol Buffers

- Google에서 개발한 이진 인코딩 라이브러리
- Facebook에서 개발한 Apache Thrift와 유사함
- 필드 유형, 태그 번호를 단일 바이트로 압축하여 더 많은 공간 절약
- 스키마 진화성 관리 제공
  - Field tags가 설정된 값만 인코딩 지원

#### Avro

- Hadoop의 하위 프로젝트로 시작한 이진 인코딩 형식
- 2가지 스키마 언어 제공
  - Writer Schema(Avro IDL): 사람이 쓰기/편집 하기 위한 언어
  - Reader Schema: JSON 기반으로 기계가 더 쉽게 읽을 수 있도록 설계
- 가장 압축률이 높음
- 스키마 진화성 관리 방식
  - Writer Schema에만 있고, Reader Schema에는 없는 값은 무시됨
  - Reader Schema에만 있고, Writer Schema에는 없으면 선언한 기본 값으로 채워짐
  - 기본 값을 설정하지 않으면 union 타입을 사용하여 null 명시
  - 인코딩 된 모든 기록의 시작 부분 번호를 저장, 데이터베이스에 스키마 버전 목록 저장하여 관리
    - Confluent Apache Kafka schema registry의 동작 방식
- 동적 생성된 스키마 지원

### Schema의 장점 

- 간결함
- 스키마 자체가 가치있는 문서 역할
- 스키마 데이터베이스를 유지 관리하면 전/후방 호환성 확인 가능
- 정척 타입 프로그래밍 언어 사용자는 스키마로 부터 코드 생성 가능
- 컴파일 시점 타입 검사 지원

그럼에도 불구하고, 작업을 단순하게 유지하기 위해 스키마 형식 수를 최소한으로 유지하는 것이 좋다.

### Modes of Dataflow (데이터 흐름 방식)

#### 데이터베이스를 통한 데이터 흐름

- 데이터베이스 쓰기 프로세스 : 인코딩
- 데이터베이스 읽기 프로세스 : 디코딩
- 스키마 관리 : 테이블 이동, 마이그레이션, 컬럼 추가, 적은 스키마 변경
- 데이터 아카이브
  - 주지적으로 dump 뜨고 Avro, Parquet 같은 데이터로 인코딩

#### 서비스 호출을 통한 데이터 흐름 (REST, RPC)

- 클라이언트 : API로 서버에 데이터 요청
- 서버 : API를 통해 데이터를 네트워크로 노출
- 스키마 관리 : API 버전 간 호환성 유지
  - Interface Definition Language(IDL)(OpenAPI, Swagger, gRPC)로 API 세부사항 파악
    - OpenAPI는 JSON으로 송수신 웹서비스에서 사용
    - gRPC는 Protocol Buffers를 송수신
- 로드밸런싱, 서비스 디스커버리, 서비스 메쉬를 통한 부하 분산 및 송수신 연결

#### 워크플로우 엔진을 동한 데이터 흐름(지속적 실행 및 워크플로우)

- 기능 하나를 실행하려면 여러 서비스 호출이 필요하다.
- 여러 서비스를 호출하는 과정을 단계별 순서로 나타낸 것이 Workflow이다.
- 워크플로우는 워크플로우 엔진에 의해 실행
  - 워크플로우 엔진 구성
    - 오케스트레이터 (실행될 작업 스케쥴링)
    - 실행기 (작업 트리거 되면 실행)
- Durable Execution
  - Exactly once 실행은 어떻게 달성할까
    - 서비스 기반이면 데이터베이스 트랜잭션으로 단순히 감싸는 방식으로 달성
    - Durable Execution Framework
      - 작업이 실패하면 재실행
      - 성공 후 재실쟁은 상태변경이나 외부 호출 건너 뜀과 같은 멱등성/항등성/무결성 보장
      - 모든 변경사항 log로 기록
      - 난수와 같은 비결정론적인 코드 분석, 통제 

#### 비동기 메시지 전달을 통한 데이터 흐름(이벤트 주도 아키텍처)

- 요청은 Event 또는 Message로 부름
- 발신자는 수신자가 이벤트 처리할때까지 기다리지 않음
- 이벤트는 네트워크 원결 통해 전송되지 않고, 메시지 브로커(이벤트 브로커, 메시지 큐, 메시지 지향 미들웨어) 통해 전달
- 브로커의 이점
  - 수신자가 이용 불가능하거나 과부하 상태일때 완충 역할하여 시스템 신뢰성 향상
  - 크래시된 프로세스에 메시지 다동으로 재전달하여 손실 방지
  - 서비스 검색이 필요하지 않아서 서비스 메시 같은 작업 필요 없음
  - 동일한 메시지 여러 수신자에게 전송 가능
  - 발신자와 수신자를 논리적으로 분리

##### 메시지 브로커
  - RabbitMQ, ActiveMQ, HornetQ, Apache Kafka 같은 소프트웨어
  - 2가지 패턴으로 사용
    - 하나의 프로세스가 명명된 Queue에 메시지 추가하면, Queue의 소비자중 하나가 메시지 수신 (여러 소비자가 있으면 그중 하나가 수신)
    - 하나의 프로세스가 지정된 Topic에 메시지를 게시하면, 브로커는 해당 메시지를 topic 구독자에게 전달 (여러명의 구독자면 모두가 메시지 수신)
  - 스키마 관리
    - Protocol Buffers, Avro, JSON, AsyncAPI(OpenAPI의 메시지 버전) 
  - 메시지는 소비 후 삭제, 이벤트 소싱으로 사용하려는 경우 무기한 저장하도록 구성

#### Distributed Actor Framework (분산 액터 프레임워크)

- Actor Model : 동시성을 위한 프레임워크
  - 스레드 및 경합조건, 잠금, 교착 상태 같은 문제를 직접 다루기 보단, 논리를 Actor에 캡슐화
    - 각 Actor는 하나의 클라이언트 또는 Entity state를 가진 Actor들이 메시지로만 소통
    - 자기 상태를 혼자만 접근
    - 외부와는 메시지로만 통신
    - 한 번에 하나의 메시지만 처리
  - 기본 규칙 
    - Actor가 메시지를 받으면 할 수 있는 일은 3가지 뿐
      - 자신의 상태 변경
      - 다른 Actor에게 메시지 전송
      - 새로운 Actor 생성
  - 특정 오류 시나리오에서 메시지 손실 있음
- 분산 액터 프레임워크란? 
  - Actor를 여러 서버에 분산되어 실행되도록 도와주는 프레임워크다. 
  - 분산·동시성 문제를 다루기 위한 “강력한 추상화 기법”
  - Event Sourcing 같은 개념과 대치되는 것이 아니고 병행
  - 복잡성을 메시지 기반 상태 머신으로 추상화한 기법 
- Akka, Orleans, Erlang/OTP 같은 문산 액터 프레임워크에서는 이 프로그래밍 모델로 애플리케이션을 여러 Node러 허ㅣㄱ징


## 중요 용어

| 용어                          | 설명                                |
|-----------------------------|-----------------------------------|
| Serialization               | 인메모리 객체를 바이트 시퀀스로 변환              |
| Encoding                    | = Serialization, 마샬링              |
| Schema Evolution            | 스키마 변경에 대응하는 것                    |
| Backward Compatibility      | 새 코드가 이전 데이터를 읽을 수 있음             |
| Forward Compatibility       | 이전 코드가 새 데이터를 읽을 수 있음             |
| Protocol Buffers            | Google의 이진 직렬화 포맷, gRPC 기본 포멧     |
| Avro                        | Hadoop 하위 프로젝트로 시작한 이진 인코딩 포멧     |
| Actor Framework             | 비동기 분산 복잡성을 상태 머신과 메시지로 추상화 하는 기법 |
| Workflow                    | 특성 목적 달성을 위해 필요한 단계별 작업의 순서       |
| Durable Execution Framework | 워크플로우 Exactly once 실행을 위한 프레임워크   |

## 질문 & 토론

- Redis 사용할때 smile같은 이진 인코딩 사용하여 성능 최적화 vs JSON으로 가독성 있게 사용 
  - TTL 전략에 따라 다를 것 같음
    - Short-lived Cache (10s~5m): 사람이 볼 필요 없는 데이터일 경우 일 것 같아서 이진 인코딩 처리 
    - Session, State Cache(30m~24h): 봐야하는 데이터인지 아닌지 따라 선택 
    - Long-lived Cache(수 시간~수 일): 운영상 많이 볼 것 같아서 JSON 포멧
  - 초당 요청이 매우 많아서 성능 최적화 필요한 경우 이진 인코딩

## 실습 / 예제

-

## 참고 자료

- 

## 느낀 점

- 이벤트를 생각할때 Queue와 Topic의 구분을 읽고 머리 꽝하는 느낌을 받았다.
  - 카프카 Topic이 왜 topic이고 publisher, subscriber 용어가 쓰이는지 너무 알 것 같다.
- Actor Model 기반으로 프로젝트가 구현 된 것 같다. 좀더 명확한 추상화 기법을 머리에 넣고 생각할 수 있을 것 같다.
- 뭔지 모르고 쓰고 있는 Avro에 대해서 자세히 알 수 있어 좋았다. 
- gRPC와 Protocol Buffers에 대해서 간접적으로 알 수 있어 좋았다.
