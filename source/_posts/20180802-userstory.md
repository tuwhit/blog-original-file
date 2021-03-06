---
title: Principles of Effective Story Writing - The Pivotal Labs Way
date: 2018-08-01 20:51:29
photos:
 - /images/pivotal.png
tags:
- PivotalTracker
- PivotalLabs
- 유저중심스토리
---
----------------------
[pivotal labs에서 pivotal tracker를 이용하여 효과적으로 스토리를 작성하는 방법](https://www.pivotaltracker.com/blog/principles-of-effective-story-writing-the-pivotal-labs-way/)에 대한 아티클을 대충 번역해서 정리해봄.

![](/images/pivotal.png)

### 스토리란?

대화와 그 문맥에 대한 placeholder
작은 디테일들을 포함할 필요가 없으나, 이야기한 내용을 상기시켜야 하며 모든것을 high level에서 포함해야함
how 가 아닌 what 이 진술되어야 함
기술적으로 규범화된것이 아니므로, 엔지니어링 팀이 문제의 기능을 구현할 수있는 정확한 정보를 지정할 필요가 없음
기능의 이유와 그 기능이 무엇인지 정확하게 설명해야함
모든 스토리는 INVEST 모델을 따라야함

#### INVEST model
Independent : 의존되지않고 릴리즈 할수있어야함
Negotiable : 토론할 준비가 돼있고 팀 인풋에 따라 조정될수있음
Valuable : end user 에게 가치를 제공함
Estimatable : 개발팀은 스토리의 복잡성을 추정할 수 있음
Small : 실제 가치를 제공하는 선에서 가능한 작아야함
testable : 테스트의 승인 기준을 포함



### 다양한 스토리 타입들
#### 기능 / 사용자 스토리 (feature)
기능 스토리는 유저에게 가치를 제공하는 제품에 추가될수 있는 가장 작은 추가적 기능의 누구에게, 무엇을, 왜를 설명할수 있도록 디자인됨
기능 스토리는 개발팀에 의해 포인트가 매겨지고 기능을 완료하는데 걸리는 시간이 아닌 복잡도로 평가됨
유저의 관점에서 작성되고 개발팀의 가벼운 요구사항 문서로서의 역할을 함
INVEST 모델에 따라 이들은 독립적이어야하며 사용자에게 확실한 가치를 제공해야함
##### feature에 포함되어야 하는 것들
- title
  - 제목은 짧아야하며 설명가능하고 특정 사용자나 개인을 포함해야함
  - 예를들어 사용자/개인은 단순히 ‘사용자’가 아닌 특정 타입의 사용자거나 개인이름(예: 토마스)이어야 함
  - 사람이 아니라 시스템이 사용자인 경우에도 동일하게 작용함(예: 구매 API)
- business case
  - 누가, 왜, 무엇을 원하는지를 서술
  - 팀의 모든 구성원들이 해당 기능이 추가되어야하는 이유를 이해할 수 있어야함
  - 이유를 생각할수 없다면 그 기능이 포함되어야 하는지 다시 판단해봐야함
  - 비지니스 케이스를 통해 팀원들은 제공된것보다 나은 사용자 경험이 있는지 생각해볼수 있음
- acceptance criteria (수락기준?)
  - 스토리가 완료되었는지 확인하기 위해 따라야할 사항을 정의함
  - 해당 스토리를 작업(?)한 개발자는 그것을 제공하기전에 수락 기준을 따라야함
  - syntax
    - GIVEN [필요한 context] WHEN [action] THEN [reaction]
    - 수용 기준에 여러번의 ‘and’를 쓰는걸 발견한다면 스토리를 더 쪼개야 함
- notes
  - 스토리에 필요한 추가적인 정보를 포함함
  - ex: 디자인노트, 개발자 노트...
- resources
  - 기능스토리를 전달하는데 도움을 주는 것들
  - ex: 목업, 와이어프레임, 유저플로우, 링크 등
- labels
  - 스토리들을 그룹핑하는데 효과적임
  - ex: Epics, 빌드 넘버, 사용자...

#### bug
버그는 이미 수락된 feature의 결함
버그를 사용해서 새 기능을 자세히 설명하면 안됨(ex: 가격은 음수가 아니어야함, 로그인 버튼이 작동하지않음)
버그들은 이미 전달된 기능과 직접적으로 연관돼있으며 새로운 유저 가치를 제공하지 않으므로 포인트가 없음. 왜냐하면 추정하기가 불가능하고 해결하는데 30초가 걸릴수도 30일이 걸릴수도 있기때문
##### bug에 포함되어야 하는 것들
- title : 짧고 설명가능해야함
- description : 현재 무슨일이 일어나고 있는지, 무슨 일이 일어나야 하는가를 서술
- instructions : 버그를 재현하는 단계를 요약하셈
- resources : 스크린샷이나 버그를 설명하는데 도움을 주는 것들

#### chore
chore는 필요하지만 사용자에게 직접적이고 분명한 가치를 제공하지는 않음 (ex: 테스트환경을 위해 새 도메인 및 와일드카드 SSL 인증서를 설치, 시스템 문제해결을 위한 툴 평가)
chore는 유저 가치에 직접적으로 기여하지 않으므로 측정할수 없음
chore가 유저가치를 제공하는것처럼 느껴지면 featrue stroy에 통합되어야함
예를들어 분석서비스를 사용하는 경우 서비스 설치에 관한 추가적인 복잡성은 chore로 분리되지 말고 feature story에 고려되어야함
##### chore 스토리에 포함되어야 하는 것들
- title : 짧고 설명가능해야함
- description : 왜 필요한지, 이것이 팀을 더 빠르게하거나 처리되지않으면 코드베이스에서 문제를 일으킬수 있는 의존성이 있는가를 서술
- resources : 작업 수행을 돕는 지침, 추가 컨텍스트등
