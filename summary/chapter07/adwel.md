# 빌드
`
마이크로 서비스를 사용하기 위한 빌드 파이프 라인 및 소스 코드를 관리하는 방법을 알아보자 
`

## 지속적 통합
- 코드 동기화 및 새로운 코드와 기존 코드가 적절한게 통합되는지 확인
- 분석과 테스트로 코드 품질에 대한 빠른 피드백을 얻을 수 있음
- IAC를 사용한다면 모든 코드의 버전을 제어해 투명성을 향상 시키고, 복제하기 쉬어짐

### 실제로 CI를 하고 있는가?
- 코드가 메인라인 브랜치에 통합 되어야 함
- 변경사항을 검증하는 일련의 테스트가 있어야 함
- 빌드 실패 복구 과정이 관련 되지 않은 체크인을 중지해서는 안됨 (빌드 때문에 병합이 지연)

### 브랜치 모델
- 다른 사람이 수행하는 작업을 방해하지 않고 독립적으로 개발 가능
- 기능 개발을 위해 장기 브랜치를 사용하지 말고 트렁크 기반 개발을 고려
- 브랜치를 사용해야 한다면 작은 단위로 짧게 유지하는게 좋음

## 빌드 파이프 라인과 지속적 제공
- 빌드 파이프 라인으로 모델링 하여 소프트웨어의 진행 상황을 추적하는 방법을 제공 (컴파일 -> 테스트 -> 운영)
- 지속적인 제공은 빌드 파이프라인 개념 기반
- 지속적인 배포는 지속적인 제공을 자동화 한 것
 
### 도구
- 파이프라인을 정의하고 시각화해 소프트웨어가 운영 단계에 이르는 전체 경로를 모델링 할 수 있음

### 절충점과 환경
- 빌드 파이프 라인은 운영환경에 대한 빠른 피드백을 받을 수 없음
- 로컬 환경은 빠른 피드백은 가능하지만 운영환경과는 많이 다름
- 피드백과 운영환경 사이에서 절충점을 검토

### 산출물 생성
- 한 번만 빌드로 결과물을 산출해야 함, 같은 것을 반복해서 빌드하면 안됨
- 검증환경과 운영환경에서 동일한 빌드의 결과물을 사용해야 함
- 위 방식을 하기 위해서는 Config는 외부에 보관되어야 함

## 소스 코드와 빌드를 마이크로 서비스로 매핑
### 거대한 리포지터리 하나와 거대한 빌드
- 모든 코드를 하나의 리포지터리로 묶어서 한 번의 빌드 여러 서비스로 배포
- 단일팀이 모든 작업을 수행하는 락스템 릴리스 환경이라면 적합
- 한 서비스 코드의 변경으로 모든 서비스에 영향을 미침(빌드, 검증 실패 등등) 
- 마이크로 서비스에서는 적합하지 않음

### 마이크로서비스당 하나의 리포지터리
- 마이크로 서비스당 하나의 레포지터리
- 비즈니스에 대한 강력한 소유권 모델을 고려하는 경우 적합
- 개발자는 여러 리포지터리를 관리하고 작업해야 함

#### 리포지터리 간 코드 재사용
- 리포지터리간 중복코드를 라이브러리로 관리
- 라이브러리는 원자적으로 롤아웃 할 수 없으며 독립적인 배포 가능성을 약화 시킴

#### 여러 리포지터리에서 작업
- 호환성 때문에 둘 이상의 마이크로 서비스를 배포해야 한다면 경계를 다시 생각해 봐야함
- 멀티 리포지터리 환경은 마이크로 서비스의 경게를 강화하는데 도움이 됨

#### 적용 대상
- 서비스 경계를 넘어 많은 변경을 하고 있다면 모노레포 패턴이 적합
- 마이크로 서비스 경계가 잘 정의되어 있다면 멀티 레포가 적합

### 모노레포
- 소스 코드를 원자적 방식으로 변경할 수 있고, 쉽게 재사용 할 수 있음
- 여러 마이크로 서비스를 변경하는데 단일 커밋을 사용
- 하나의 리포지터리에 여러개의 프로젝트가 존재 빌드는 독립적으로 가능

#### 빌드 매핑
- 단일 리포지터리의 하위 프로젝트가 독립적인 빌드로 매핑
- common 프로젝트에 공통적으로 사용하는 코드를 분리하여 재사용 가능

#### 소유권 정의
- 강력한 소유권 - 외부에서 변경을 원할 시 내부에게 요청해야 함
- 약한 소유권 - 외부에서 변경 가능하지만 내부에서 검토
- 공동 소유권 - 모든 사람이 변경 가능
- 회사 규모가 커질 수록 책임에 따라 소유권을 정의할 필요가 있음
- 깃허브에 있는 CODEOWNERS 와 같은 기능이 대표적

#### 도구
- 회사 규모에 따라 모노레포를 관리 해주는 도구가 필요
- 구글을 파이퍼라는 자사 소유의 소스 제어 도구를 사용
- 마이크로 소프트는 깃용 VFS를 사용

#### 모노의 범위는?
- 팀 단위로 관리,소유한 서비스를 묶어 모노레포로 관리
- 위 방법은 공동 소유권을 실천하는 팀에게 모노레포의 이점을 확실이 제공 함

#### 적용 대상
- 대기업에서도 많이 사용하지만 모노레포를 관리하기 위해 상당한 자원을 투입 중
- 따라서 소수의 개발자와 팀이 있는 곳이 더 적합하다고 생각

### 어떤 방식을 사용해야 할까?
- 소규모에서는 모노레포, 규모가 커질 수록 멀리레포 방식이 더 적합함
- 모노레포는 소유권 경계가 불분명 해지고 그것을 관리해야 하는 도구가 필요
- 모노레포에서 멀티레포로 전환은 많은 비용이 필요함 적절할 때 바꾸는 걸 추천