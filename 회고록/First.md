

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
- SwiftUI의 뷰는 @Published 상태 변경에 따라 

4. @ObservedObject vs @StateObject






