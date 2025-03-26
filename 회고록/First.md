

# 2025.03.20

## 개인 프로젝트 구현 피드백
### SwiftUI + MVVM + Combine + Clean Architecture

1. Git
- 기능 구현 브랜치 분리 제대로 할 것.
  - 구현 내용 중 프로젝트에 Firebase를 활용한 SNS 로그인(애플, 구글) + 전화번호 로그인이 있다.
  - 포괄적으로 보면 동일한 로그인 기능이지만, 동일 브랜치에 한꺼번에 반영하게 되면, 커밋 내용 및 PR 내용이 방대해져 피드백이 어렵다.
  - 해당 부분 유의해서 구현할 것.

2. Combine(CurrentValueSubject...) vs @escaping
- 요약하자면, 해당 스트림을 구독하는 객체의 수에 따라 사용 여부를 결정한다.
  - 비지니스 로직을 거치고, 해당 결과값을 수신하는 대상이 하나라면 즉, 1대1 매칭일 경우에는 @escaping을 사용하는 것이 적절하다.
  - 예를들어, 특정 로직을 수행하는 Usecase 같은 경우, 해당 UseCase를 필요로 하는 여러 ViewModel에서 결과값을 수신할 경우에는 Publisher를 반환해 해당 스트림을 구독하는 방향으로 설계하는 것이 옳다.


1. MainThread - @Published
- SwiftUI의 뷰는 @Published 상태 변경에 따라 body 가 다시 그려진다. 즉, @Published 프로퍼티 래퍼는 UI와 밀접한 관련이 있다. @Published의 프로퍼티 변경에 따라, UI를 업데이트 해야한다면, 메인스레드에서 해당 과정을 진행할 수 있도록 해야한다.

1. @ObservedObject vs @StateObject
- 두 프로퍼티 래퍼 모두 ObservableObject 프로토콜을 준수하는 객체의 변화(ex: @Published 프로퍼티)를 감지하고, 그에따라 View를 업데이트 하는데 사용된다.
- 해당 내용을 찾아본 결과, 값이 변경되면 ObservableObject 프로토콜 내 objectWillChange publisher의 send()라는 메서드를 자동으로 호출하여 데이터가 변경되었음을 알릴 수 있다고 한다.
-  하지만, 두 프로퍼티 래퍼는 차이점이 존재한다.
- 먼저, @ObservedObject의 경우 프로퍼티의 변화가 생길 경우, 뷰를 다시 생성해서 그린다. ViewModel을 @ObservedObject로 선언할 경우, 뷰를 다시 그리며 ViewModel 인스턴스를 초기화 한다.
- 반변에, @StateObject의 경우 프로퍼티 변화가 생길 경우, 뷰를 다시 생성해서 그리진 않는다.
- 보통, @StateObject를 사용하며, 하위 뷰에 프로퍼티를 전달해서 사용할 경우, 의존성을 줄이기 위해 하위뷰에는 @ObservedObject로 프로퍼티를 만들어 상위 뷰에서 전달해 사용한다.





