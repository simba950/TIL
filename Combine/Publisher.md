
# Publisher

## Publisher?
-> Publisher는 Combine 프레임워크에서 제공하는 **비동기 데이터 스트림을 생성하는 객체**다.
-> **특정 값이 변경될 때마다 새로운 값을 방출**하는 역할을 함.
-> Publisher가 방출하는 데이터를 받아서 처리하는 게 Subscriber다.

```swift
protocol Publisher {
    associatedtype Output
    associatedtype Failure: Error

    func receive<S>(subscriber: S) where S : Subscriber, Self.Failure == S.Failure, Self.Output == S.Input
}
```
- Output : 방출하는 값의 타입
- Failure : 실패 시 반환할 에러 타입
-> 특정 타입의 값을 주기적으로 내보낼 수 있고, 실패할 수도 있는 존재

---

## NotificationCenter

<p align="center" style="color:gray">
  <img style="margin:10px 0 10px 0" src="image.png" alt="factorio thumbnail" />
  이미지출처: https://sidongmen.tistory.com/22
</p> 

  - 등록된 옵저버에 이벤트가 발생했을 때, 감지해서 해당 이벤트를 전달하는 메커니즘이라 한다. (공식문서 번역 이슈...ㅠ)

    >[!NOTE]
    > **갑자기 웬 NotificationCenter?**
    >```swift
    > //...
    >// 기존 NotificationCenter
    >// 입력 키보드가 나타날 때, 이벤트 발생
    >NotificationCenter.default.addObserver(
    >    self,
    >    selector : #selector(doSomething),
    >    name : UIResponder.keyboardWillShowNotification,
    >    object : nil)
    >
    > // with Combine
    > NotificationCenter.default.publisher(for : UIResponder.keyboardWillShowNotification)
    >.sink { _ in
    >    // doSomething
    > }
    > .store(in: $cancellables)
    > //...
    >```
    > - 기존 NotificationCenter를 Combine을 활용해 이벤트를 방출하는 Publisher로 구현할 수 있다.

## @Published

-> Swift에서 제공하는 Property Wrapper 중 하나이다.
```swift
// 연산 프로퍼티
class Cat {
    var croppedClawCount : Int = 0 {
        didSet {
            print("자른 발톱 수 : \(croppedClawCount)")
        }

        willSet {
            print("자를 발톱 수 : \(newValue)")
        }
    }
}
let blackCat = Cat()
cat.croppedClawCount = 4

// Published Property Wrapper
class Dog {
    @Published var croppedClawCount : Int = 0
}

let whiteDog = Dog()

whiteDog.$croppedClawCount
    .sink { count in
        print("자른 발톱 수 : \(count)")
    }
    .store(in: $cancellable)
```
- didSet, willSet은 인스턴스 내부에서만 값의 변경을 감지할 수 있다.
- 하지만, @Published의 경우 Combine을 활용할 수 있으며, 인스턴스 외부에서도 값 변경을 감지할 수 있다.(sink를 사용해 구독.)

## Sequence Publisher
```swift
import Combine

// Sequence Publisher - Array
[1,2,3].publisher
    .sink { completion in
        switch completion {
        case .finished:
            print("완료")
        case .failure(let error):
            print("에러 발생")
        }
    } receiveValue: { num in
        print(num)
    }

// Sequence Publisher - Set
Set([1,2,3]).publisher
    .sink { completion in
        switch completion {
        case .finished:
            print("완료")
        case .failure(let error):
            print("에러 발생")
        }
    } receiveValue: { num in
        print(num)
    }

// Sequence Publisher - Dictionary
["A" : 1, "B" : 2, "C" : 3].publisher
    .sink { key, value in
        print("key: ", key, "value: ", value)
    }

```


## Just
```swift
import Combine

Just("Hi, there !")
    .sink { string in
        print(string)
    }

Just(3)
    .sink { number in
        print("\(number)")
    }

```
- 구독자에게 값을 한 번만 방출하고 완료되는 Publisher다.
- Just Publisher는 error로 실패하지 않는다.

## Empty
```swift 
import Combine

let emptyPublisher = Empty<Int, Never>(completeImmediately: false)

emptyPublisher.sink { completion in
    print(completion)
} receiveValue: { num in
    print(num)
}

```
- 어떤 값도 방출하지 않는 Publisher
- completeImmediately 값(Bool)의 여부에 따라 finished 여부를 설정할 수 있다.
completeImmediately -> false : finished 되지 않음

## Fail
```swift
import Combine

enum MyError : Error {
    case myError
    case otherError
}

let failPublisher = Fail<Int, MyError>(error: .myError)

failPublisher.sink { completion in
    switch completion {
    case .finished:
        print("Fail Publisher not finished")
    case .failure(let error):
        print("Error : \(error)")
    }
} receiveValue: { num in
    print("Fail Publisher not emit value!")
}
```
- 에러를 방출하는 Publisher
- 지정한 에러 값으로 종료시킨다.

## Future
```swift
import Combine

let futureData = Future<Data, Never> { promise in
    // 내부에 비동키 코드를 넣어 callback 함수를 구현할 수 있다.
    URLSession.shared.dataTask(with: URL(string: "https://www.naver.com")!) { data, response, error in
        if let data = data {
            promise(.success(data))
        }
    }
    .resume()
}
.sink { data in
    print("\(data)")
}

// deferred 활용 -> 구독자가 없을 경우 클로저는 실행이 안됨.
let deferredFutureData = Deferred {
    Future<Data, Never> { promise in
        // 내부에 비동키 코드를 넣어 callback 함수를 구현할 수 있다.
        URLSession.shared.dataTask(with: URL(string: "https://www.naver.com")!) { data, response, error in
            if let data = data {
                promise(.success(data))
            }
        }
        .resume()
    }
}
```
- 클로저를 통해 일정 작업을 수행하고, 그 콜백을 단일 값으로 방출하고 싶을 때 사용.
- Future 퍼블리셔는 구독자 여부에 상관없이 내부 클로저를 실행함
  - Deferred Publisher를 활용해서 구독 시점에 클로저를 실행할 수 있도록 만들 수 있다.
- Future 퍼블리셔의 경우 promise로 value나 error를 방출하면 finished 됨
  - 즉, promise로 한 번 방출하면, 이후 값이나 에러를 방출해도 구독자가 해당 값을 받지 못한다.