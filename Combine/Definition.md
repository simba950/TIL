
# Definition
## Combine?

- Combine은 시간에 따른 값을 처리하기 위한 선언적(declarative) Swift API를 제공한다.
- Combine은 시간이 지남에 따라 변경될 수 있는 값을 노출하도록 퍼블리셔를 선언하고, 구독자는 퍼블리셔로부터 해당 값을 수신하도록 선언한다.
- Declarative -> 추상화 레벨을 올려서 내부의 절차는 감추고, 원하는 결과 중심으로 프로그래밍.

## Stream
- Stream은 데이터의 흐름을 의미한다. 즉, 시간에 따라 변하는 값의 연속적인 흐름을 나타낸다.
- 이벤트, 상태변경, 네트워크 응답 등 다양한 데이터 흐름을 표현할 수 있다.

### Upstream + Downstream
  1. upstream : 데이터를 생성하고 전달하는 쪽
  - map, filter, flatMap 같은 연산자는 upstream을 조작해 downstream으로 데이터를 전달한다.
  
  2. downstream : upstream에서 내려온 데이터를 받아서 처리
  - sink를 통해 데이터를 소비
  - UI 업데이트, 네트워크 응답 처리 등이 downstream에서 진행됨.

```Swift
import UIKit
import Combine


let upstream = [1,2,3,4,5].publisher

let transformedStream = upstream.map { $0 * 3 }

// transformedStream에서 데이터를 가공해서 전달.

// ⭐️ Upstream은 Downstream이 구독(Subscribe)해야 동작한다. (lazy 방식)
let last = transformedStream.sink { value in
    print("stream tutorial : \(value)")
}

```

## Publisher<Output, Failure>

```swift
// document
protocol Publisher {
    associatedtype Output  // 스트림에서 방출할 데이터 타입
    associatedtype Failure: Error  // 실패 시 반환할 에러 타입

    func receive<S>(subscriber: S) where S : Subscriber, Self.Failure == S.Failure, Self.Output == S.Input
}
```

- 데이터 스트림을 생성하고, 전달하는 역할을 한다.
- Output - Publisher가 방출하는 데이터.
- Failure - Publisher가 실패할 때 발생하는 에러타입.
