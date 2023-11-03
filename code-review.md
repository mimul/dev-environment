코드 리뷰에 필요한 것들.
======================
코드 리뷰는 개발자의 스킬 향상 뿐만 아니라, 개발 문화 형성에 아주 중요한 요소라고 생각한다. 그런 관점에서 코드 리뷰가 지속가능한 문화로 자리를 잡게하기 위해서 필요한 것들을 여기에 정리한다.

#### 코드 리뷰의 기본 원칙들
여기서는 코드리뷰에서 따라야할 기본 원칙들을 정리한다. 될 수 있으면 최소한의 원칙만 작성하는 것으로 한다.

- 리뷰도 프로젝트의 공식 일정과 자원에 포함시킨다.
- 목표는 사람(사람에 대한 선입견 포함)이 아니라 프로그램의 결과물을 검토한다.
- 문제를 명확하게 할 순 있어도 모두 해결하려고 하지 않는다.
- 성능과 이해도에 영향을 주지 않는 한 그 사람의 스타일을 문제제기하지 않는다.
- 세세한 버그를 볼 것이 아니라, 전체 구성과 흐름에 문제가 없는지 주목하자.
- 리뷰는 2시간을 넘지 않는다.
- 초기에는 비공식 리뷰나 교육을 자주하고 일정시간이 지나면 공식 리뷰로 전환한다.
- 필요하면 체크리스트를 만들어서 리뷰에 임한다.
- 토론을 장려하고 반론은 대다수가 동의가 이루어졌거나 대상자가 불특정 다수가 되었을때 진행하자.

#### 용어 정의
용어는 굳이 외우려하지 말며, 리뷰에서 넓은 관점을 유지하지 위해 정리한다. 리뷰에서 공식적으로 거론하지는 않으며 강요하지도 않는다.

- DC(Duplicate Code) - 중복된 코드(중복된 코드나 조건들).
- BPC(Bad Performance Code) - 성능에 좋지 않는 코드.
- BN(Bad Name) - 변수나 클래스, 함수 등에 너무 길거나 가독성 떨어지고 일관되지 않은 이름.
- VS(Violates Specifications) - 가이드나 지침을 따르지 않음.
- TCL(Too Complicated/Larged) - 불필요하거나 너무 크고 복잡한 함수나 클래스.
- LP(Long Parameter) - 불필요하거나 너무 긴 인자값들.
- II(Inconsistent Indentation) - 비일관적인 들여쓰기.
- NEC(Not Enough Test Cases) - 테스트 케이스가 너무 적음.
- TMIS(Too Much If/Switch) - 조건 분기가 많음.
- NC(No Comments) - 주석이 너무 없음.
- SS(Shotgun Surgery) - 하나의 함수나 클래스를 변경했는데 많은 것들이 영향을 받는다.
- FE(Feature Envy) - 다른 클래스의 속성과 메소드들을 사용해 다른 클래스와 강한 관계가 성립한다. 모듈(클래스) 독립적이지 못하다.
- BC(Boilerplate Code) - Getter/Setter 등 맹복적으로 추가되는 반복적인 코드들.
- TE(Throws Exception) - 예외 처리.
- NG(Not General) - 너무 범용적이지 않음.
- DD(Dead Code) - 사용되지 않거나 미리 만들어진, 불필요한 코드들.
- MN(Magic Number) - 매직 넘버.
- PD(Platform Dependency) - 플랫폼(언어, 환경 등) 종속성이 있다.
- AOS(Abuse Open Source) - 오픈 소스 남용.

#### 라벨링
벨을 붙이면 액션을 명확하게 전달할 수 있고, 코멘크의 우선순위가 명확해져 효율성을 가져올 수 있다. [GihubDocs >Setting >회신템플릿 만들기](https://docs.github.com/ja/get-started/writing-on-github/working-with-saved-replies/creating-a-saved-reply)로 처리하면 외우지 않아도 된다.
| 라벨 | 의미                        | 의도                        |
| :-- |:--                         |:--                         |
|Q   | 질문 (Question)              | 질문. 상대방은 답변이 필요합니다.  |
|FYI | 참고로 (For your information) | 참고로 공유. 액션은 불필요       |
|IMO | 제안 (In my opinion)         | 개인적인 견해와 제안.            |
|MUST| 필수(Must)                   | 이것을 고치지 않으면 승인할 수 없다.|
