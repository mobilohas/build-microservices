# 모니터링에서 관찰가능성으로

`
모니터링과 관찰가능성에 대한 차이점을 이해하고 
모니터링 환경을 구축할 때 고려해야 할 것들을 알아보자
`

## 분열, 공황, 그리고 혼란
- 마이크로 서비스 모니터링 환경은 더 큰 그림을 볼 수 있도록 집계를 제공해야 함
- 운영 환경에서 테스트한다는 개념을 수용해 시스템 정상 상태에 대한 더 현명한 사고 방식을 가져야 함 (요청 처리 시간..)

## 단일 마이크로서비스, 단일 서버
- 매우 간단한 구성, 호스트가 하나이므로 인스턴스 자체에 접근하여 모니터링 해도 어려움이 없음

## 단일 마이크로서비스, 다수 서버
- 여로 호스트의 메트릭, 로그를 집계하고 분석해 줄 도구가 필요하게 됨
- 로드 밸런서의 모니터링 및 서비스에서 장애가 났을 시 적절한 트래픽 분배도 필요함

## 다수 마이크로서비스, 다수 서버
- 매트릭과 로그 뿐만 아닌 유입되는 데이터를 선별하고 트랜잭션이 어디로 이어지는지 추적할 수 있어야 함
- 관찰 가능성 구현과 운영환경에서의 테스트로 사고방식을 전환

## 관찰가능성 대 모니터링
- 관찰가능성은 외부 출력으로부터 시스템의 내부 상태를 이해할 수 있는 수준을 의미
- 소프트웨어를 서로 다른 개체의 집합이 아닌 시스템으로 봐야 함

### 관츨가능성의 주축? 그다지 빠르지 않다.
- 관찰 가능성은 시스템의 속성이며, metric, event, log, trace 와 같은 개념에 묶여서는 안됨 (포함되긴 함)
- 시스템을 관찰 가능하게 만들려고 할 때는 이벤트 측면으로 수집하고 필요할 출력을 생각해야 함

## 관찰가능성의 구성 요소
- 문제가 발생하면 시스템을 정상화 할 수 있도록 충분한 정보가 필요
- 또 문제가 발생한 이유와 개선해야할 지점을 알 수 있어야 함

### 로그 집계
- 마이크로 서비스에서는 도구를 사용하여 로그를 수집하고 중앙에서 확인할 수 있어야 함
- 로그는 운영 시스템에서 벌어지는 일을 이해하도록 돕는 가장 중요한 메커니즘
- 로그 집계는 마이크로 서비스를 구현하기 위한 전제조건, 그만큼 유용하고 중요

#### 공통 포맷
- 합리적인 표준 로그 형식을 만들어 기록해야 함
- 로그에서 필요한 정보를 쉽게 분석하고, 쉽게 추출 하도록 집계 도구를 구성해야 함

#### 로그 문자열의 상관관계
- 마이크로 서비스에서 발생하는 모든 활동에 관한 로깅은 동일한 상관관계 ID로 설정해야 함
- 상관관계 ID는 상호작용이 발생하는 트랜잭션을 추적하고 식별하는데 필요

#### 타이밍
- 로그의 타임스탬프는 서비스가 실행중인 인스턴스의 시간을 기준으로 작성 됨
- 따라서 마이크로 서비스에서는 로그 시간 오차를 완벽하게 제어하기 어려움
- 분산 추적 도구를 사용하여 해결해야 함

#### 단점
- 시간 왜곡으로 인해 호출이 발생한 순서가 보장되지 않을 수 있음
- 호출이 많아 질 수록 로그 데이터는 더 많아지며 비용문제로 이어짐
- 로그 집계 솔루션을 구축하고 유지하는 비용이 발생
- 민감한 데이터에 대한 보안 문제를 해결해야 함

### 메트릭 집계
- 트래픽 증가와 장애 발생을 구분할 수 있도록 오랜 시간동안 메트릭을 수집해야 함
- 용량 계획을 수립하여 시스템의 비용 효율성과 반응성을 높일 수 있음

#### 낮은 카디널리티 대 높은 카디널리티
- 낮은 카디널리티를 염두에 두고 구축된 시스템에 높은 카티널리티를 사용하려면 어려움
- 높은 카디널리티를 처리하는 시스템은 좀 더 다양한 정보를 제공해 줄 수 있음 (높은 카디널리티를 권장)

### 분산 추적
- 시스템이 복잡해짐에 상관관계의 흐름을 보는 방법이 중요함

#### 작동 원리
- 로컬 활동은 span 으로 캡처되고 고유 식별자로 동일한 span을 묶어 trace로 구성
- 샘플링을 사용하여 일부 trace만 수집하여, 오버헤드 문제를 해결할 수 있음

#### 분산 추적 구현
- 어플리케이션 내에서 수집 -> 수집기로 전송 -> 시각화 도구

### 잘하고 있나요?
- 마이크로 서비스에서 관찰 가능성 구축이 과도하게 수행하고 있는지, 모자른지 판단하기 어려움
- 필요한 리소스, 허용 가능한 응답 시간을 만드는 요소가 무엇 인지을 판단하고 그 기준으로 서비스가 정상인지 식별

#### 서비스 수준 계약 (SLA)
- 시스템을 구축하는 사람과 시스템을 사용하는 사람 사이의 계약
- 최소환의 수준으로 정하는 경향이 많기 때문에 사용자가 만족하지 못하는 경우가 있음

#### 서비스 수준 목표 (SLO)
- 팀 수준에서 서비스 수준 목표(SLO)를 정의하여 SLA를 충족해야 함 

#### 서비스 수준 지표 (SLI)
- SLO 를 충족하기 위해 데이터를 수집하는 것이 서비스 수준 지표
- 특정 프로세스의 응답 시간, 등록 중인 고객, 고객에게 제기되는 오류 또는 진행 중인 주문이 될 수 있음

#### 오류 예샨
- 시스템에서 허용되는 오류의 양을 예산을 계산
- 오류 예산은 SLO를 얼마나 잘 달성하고 있는지를 명확하게 파악하는데 도움이 됨
- 오류 예산을 초과한 경우라면 롤아웃을 연기하고 안정성을 개선해야 함

### 알림
- 문제가 발생할 때 시스템 관리자가 빠른 조취를 취하기 위해 알림을 사용
- 어떤 종류의 문제를 알리고 어떻게 알려 하는지 정확하게 파악 해야 함

#### 어떤 문제는 다른 문제보다 훨씬 심각하다
- 문제에 대한 우선순위를 잘 지정하여 사소한 일과 긴급할 일을 구분해야 함

#### 알림 피로
- 알람이 너무 많으면 문제상황을 명확히 파악하기 어려움
- 우선순위를 잘 지정하여 적절한 알람을 발송 해야 함

#### 더 나은 알림을 위해
- 관련성 - 경고할 가치가 있는지 확인
- 고유성 - 경고가 다른 경고를 복제하지 않아야 함
- 적시성 - 알림을 활용할 수 있도록 신속하게 알림을 받아야 함
- 우선순위 - 알림을 처리할 순서를 결정할 수 있도록 충분한 정보 제공
- 알기 쉬움 - 명확하고 읽기 쉬어야 함
- 진단 - 무엇이 잘못돼쓴지 명확해야 함
- 자문 - 취해야 할 조치를 할 수 있도록 도와야 함
- 집중 - 가장 중요한 문제에 주목

### 시맨틱 모니터링
- 시스템이 정상 작동하는 모델을 정의하면 오류의 우선순위를 정하는 방법을 이해하는데 도움이 됨
- 단순 서비스의 기능이 아닌 좀 더 높은 수준의 구체적인 기준이 필요 (ex 시간당 2만달러의 제품을 판매하고 있다)

#### 실사용자 모니터링
- 실사용자 모니터링의 주요 단점은 노이즈가 많다는 것, 정보가 많기 깨문에 면밀히 살펴보는 것이 어려움
- 이미 발생한 일을 알려주므로 문제가 발생한 후에야 문제를 파악할 수 있음
- 운영 환경 테스트의 또 다른 형태인 합성 트랜잭션을 사용하면 노이즈와 문제가 사전에 발견할 수 있음

### 운영 환경에서 테스팅
- 운영 환경에서 이뤄지는 다양한 형태의 테스트는 문제를 조기에 발견하는데 매우 효과적

#### 합성 트랜잭션
- 가짜 행동으로 트랜잭션을 발생시켜 테스트 하고 문제가 발생하면 경고
- 단순히 메트릭을 수집하고 경고를 보내는 것 보다 높은 수준의 지표가 될 수 있음

## 표준화
- 모니터링 및 관찰가능성은 표준화가 매우 중요한 영역
- 표준화를 지원해주는 다양한 도구가 존재

## 도구 선택
### 민주적 선택
- 모니터링 툴에 사용 난이도 및 비용 등을 고려해 도구를 선택해야 함
- 팀원들이 사용할 수 있는 소프트웨어를 선택하는 것도 중요

### 쉬운 통합
- 개방형 표준을 지원하는 도구를 선택하면 통합 작업이 쉬어지고 공급 업체를 쉽게 변경할 수 있음

### 맥락 제공
- 시간적 맥락 - 시간을 기준으로 비교
- 상대적 맥락 - 시스템의 다른 것과 관련하여
- 관계적 맥락 - 시스템의 의존성 관계
- 비례적 맥락 - 문제의 범위가 큰지, 작은지

### 실시간
- 신속하게 정보를 확인할 수 있어야 함

### 규모에 맞게
- 현재 시스템 규모와 비용 효율성을 판단하여 선택
- 규모에 따라 확장할 수 있는 도구가 이상적

## 기계화된 전문가
- 자동 이상 감지와 같은 기능이 아직까지는 인간을 대체할 수 없음
- 분산 시스템의 다양하고 복잡한 환경은 숙련되고 지원 가능한 인간 운영자가 필요
