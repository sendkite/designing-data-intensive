# Chapter 2: 비기능 요구사항(Defining Nonfunctional Requirements)

## 핵심 개념
몇가지 비기능 요구사항을 살펴본다

- 성능 측정 방법 (지연 시간, 처리량)
- 신뢰성과 장애 허용
- 확장성
- 유지보수성

## 요약

### Case Study: Social Network Home Timelines
- X(트위터) 타임라인 구현 사례
    - Polling 방식의 문제점
        - 너무 많은 유저 알림
        - 너무 빈번한 데이터 조회 (초당 200만 번 쿼리)
        - 특정 사용자는 초당 4억번의 조회
        - 느려지는 속도
    - Materializing and Updating Timelines
        - 온라인 팔로워에게 push 방식으로 새 게시물 알림
            - Fan-out 방식으로 follower에게 게시글 delivery
        - 미리 쿼리 결과를 캐시 (Materializing)
            - 유명인의 타임라인, 일반인 타임라인을 별도 구성하고 조합하는 방식도 가능
            - 일반인 타임라인도 Materializing하게 되면 쓰기 부하가 큼

### Describing Performance
소프트웨어의 성능 지표 2가지
- Response Time
- Throughput

처리향이 한계에 다다르면 CPU 큐잉으로 응답속도가 느려지고, 처리령도 현저히 낮아짐
- 한계 도달하지 않도록 안전장치 필요함
    - 토큰 버킷 알고리즘
    - 서킷 브레이커
    - Backpressure 관리 기법 적용
        - 큐 기반 버퍼링
        - 속도 제어(Throttling)
        - 소비자 처리할 수 없음 신호 전달
        - 로드 밸런싱
    - Scale 하여 사용할 수 있는 자원을 늘린다

![처리량-응답시간-큐잉](../../static/rs-tps-relationship.png)

#### Latency and Response Time

- Response Time : 요청 받아서 응답까지 걸리는 모든 지연 시간
- Service Time : 사용자 요청 처리하는 시간
- Queueing delays : 요청이 많아서, 응답 패킷을 네트워크 전송 전 버퍼링 시간 (Head-of-Line Blocking)
- Latency : 요청이 처리되지 않는 시간 (ex. 네트워크 통과 시간)

![처리량-응답시간-큐잉](../../static/response-time.png)

#### Average(평균), Median(중앙 값), and Percentiles(백분위)

- 응답 시간은 요청마다 달라 측정 값의 분포로 생각해야한다.
    - 주로 xxx건의 응답 시간 평균, 백분위수로 이상치 확인
    - 중앙 값은 평균의 중간 값 의미

![백분위수](../../static/rs-median.png)

응답시간의 중요성
- Akami : 응답 시간이 100ms 증가하면 전자상거래 사이트의 전환율이 최대 7%까지 감소
- Google : 2006년 구글은 검색 결과 응답 시간이 400ms에서 900ms로 느려지면 트래픽과 수익이 20% 감소
- Bing : 로딩 시간이 2초 증가하면 광고 수익이 4.3% 감소

응답시간 백분위 수로 모니터링 필요함
- 응답속도 히스토그램 구성(막대 그래프)
- 라이브러리 (HdrHistogram, t-digest, OpenHistogram, DDSketch)

### Reliability and Fault Tolerance
- Fault vs Failure
    - Fault : 시스템 특정 부분 동작하지 않음, 외부 서비스 장애
    - Failure : 시스템 전체 장애 (SLO - service level objective 미달)

- Fault Tolerance (내결함성)
    - 특정 오류가 발생해도 사용자에게 필요한 서비스를 계속 제공하는 시스템을 내결함성이 있다고 한다.
- single point of failure (SPOF),
    - 특정 오류가 시스템 전체 오류가 되는 부분

> 내결함성을 갖추려면 요청의 누락, 중복 없이 작업 인계 필요한데
> 이를 exactly-once semantics 이라고 함

- 카오스 엔지니어링이라고 하여 오류를 일부러 주입하여 내결함성 강화할 수 있음

#### Hardware and Software Faults

하드웨어의 오류
- 자기 하드 드라이브는 매년 약 2-5% 고장
- SSD는 매년 약 0.5-1% 고장
- 1000대중 1대는 제조 결함으로 CPU 코어 잘못된 경우도 있음
- RAM 데이터는 내년 1% 데이터 오류
- 데이터 센터 불능 가능성 (자연재해)

하드웨어는 이중화(redundancy)로 내결함성을 갖춘다.

소프트웨어 오류
- 운영환경 시스템 동작 측정, 모니터링 및 분석으로 예방

인간의 오류
- 철저한 테스트, 롤백 메커니즘, 점진적 배포, 모니터링으로 영향 최소화
- a culture of blameless postmortem (비난 없는 사후처리 문화)

### Scalability
- 시스템이 특정 방식으로 성장할 경우, 그 성장에 대처하기 위해 우리의 선택지는?
- 리소스는 어떻게 늘리며, 현재 아키텍처의 한계에 도달하는 시점은?
- 시스템이 부하가 발생하는 곳은? 부하가 2배가 된다면?
    - 성능을 유지하려면 리소스를 얼마나 늘려야할까?

#### Shared-Memory, Shared-Disk, and Shared-Nothing Architecture
- 수평 확장, Scale out
- 장점
    - 선형적 확장 가능
    - 하드웨어 리소스 쉽게 조정
    - 시스템 여러 센터, 지역 분산하여 내결함성 구축
    - 가격 대비 우수한 하드웨어 사용
- 단점
    - 명시적 샤딩 필요함
    - 분산 시스템의 모든 복잡성 수반

- 일부 클라우드 네이티브 시스템은 저장, 트랜젝션 위해 별도 서비스를 사용하기도 함

#### Principles for Scalability

- 빠르게 성장하는 서비스를 개발하는 경우, 부하가 한 자릿수씩 증가할 때마다 아키텍처를 재검토해야 할 가능성이 큼
- 확장성을 위한 좋은 일반적인 원칙은 시스템을 서로 거의 독립적으로 작동할 수 있는 더 작은 구성 요소로 분해하는 것
    - MSA : 샤딩, 스트림 처리, shared-nothing 아키텍처 수반
- 필요보다 복잡하게 만들지 않기

### Maintainability
- Operability: 운영 용이성
    - 자동화 (조직에 따라 일정 수준)
    - 모니터링 도구
- Simplicity: 복잡성 관리
    - 본질적 복잡성
    - 우연적 복잡성
    - 추상화, 디자인 패턴, Domain Driven Design
- Evolvability: 변경 용이성
    - 애자일 작업 패턴
    - TDD

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

- 내가 담당하고 있는 시스템의 처리량(Throughput)이 한계에 도달했을 때 어떤 안전장치가 있을까
- 우리회사 시스템에서 SPOF(Single Point of Failure)는 어디일까
- 비난 없는 사후처리 문화(Blameless Postmortem)"가 한국에서 가능할까
- 내가 담당한 시스템의 아키텍처 한계 도달 시점은 언제일까
- 내 담당 시스템의 내결함성은 어떻게 관리되고 있을까
- 우리 팀은 우연적 복잡성(Accidental Complexity)과 본질적 복잡성(Essential Complexity)을 구분하고 있는지 그리고, 부채 관리는 어떻게 하고 있는지

## 실습 / 예제

- https://github.com/sendkite/ddia-sns

## 참고 자료

-

## 느낀 점

- 구현 관점에 매몰되었던 것 같다. 인프라, 저장소, 부하 관점에서 시스템에 대해 생각해 본 것이 좋았다.
