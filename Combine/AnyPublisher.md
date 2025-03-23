
# AnyPublisher

```swift
import Combine

func publisher2() -> AnyPublisher<Data, URLError> {
    let publisher = URLSession.shared.dataTaskPublisher(for: URL(string: "https://www.naver.com"))
    // transform 방식
    //        .map { (data: Data, response: URLResponse) in
    //            return data
    //        }
    //        .map { (data: Data, response: URLResponse) in
    //            data
    //        }
    // keyPath 방식
        .map(\.data)
        .eraseToAnyPublisher()
    
    return publisher
}

```
- 내부 구현 과정 및 오퍼레이터에 따라 함수의 리턴 타입이 매번 바뀜
  - 구현 로직에 따라 매번 함수의 리턴값을 수정해줘야 하는 번거로움이 존재
- 방출하려는 값이나 에러의 타입을 기반으로 AnyPublisher 타입으로 리턴할 수 있다.

>[!NOTE]
> **개인 프로젝트 feedBack**
> 
> Clean Architecture + Combine + SwiftUI + MVVM 프로젝트
> CurrentValueSubject 및 PassThroughSubject 사용해 리턴값 자체를 타 레이어에 반환할 경우 상위 계층에서 하위 계층의 내부 비지니스 로직을 알 수 있다는 문제가 있으며, 반환값에 의존성이 짙어지는 문제가 있다.
> eraseToAnyPublisher 인스턴스 메소드를 통해 세부 구현 내용(비지니스로직)을 숨기고, 의존성을 줄일 수 있다.
> 