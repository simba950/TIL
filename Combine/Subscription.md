
# Subscriptions
```swift
import Combine

let url = URL(string: "https://www.naver.com")

let subscription = URLSession.shared.dataTaskPublisher(for: url)
.map { (data: Data, response: URLResponse) in
    return data
}
.sink { _ in

} receiveValue : { data in
    print("처리 완료: \(data)")
}

subscription.cancel()
```

-> Publisher에 Subscriber(ex: .sink)를 구현하면, Subscription이 된다.
-> 이 Subscription은 취소가 가능하다.
-> Subscription의 경우 변수에 할당이 안되고 사라지면, 바로 cancel 처리를 하게 된다.
-> 동기적인 작업으로 이루어진 Publisher는 Subscription을 하지 않아도 실행된다. 비동기는 다름.

```swift
import Combine

class Test {
    init()
    {
       let url = URL(string: "https://www.naver.com")

let subscription = URLSession.shared.dataTaskPublisher(for: url)
.map { (data: Data, response: URLResponse) in
    return data
}
.sink { _ in

} receiveValue : { data in
    print("처리 완료: \(data)")
}
    }
}

let test = Test()

```
-> 생성자를 통해 인스턴스를 생성하면, 생성자 내부 지역 변수들은 전부 해제된다.
-> 인스턴스 생성이 끝난 시점에 subscription 변수가 사라져 cancel 처리가 된다.
-> AnyCancellable 타입의 변수에 Subscription을 할당할때마다 이전 Subscription은 취소처리 되는 문제가 발생한다.
-> 위와 같은 이유로 클래스 전역 변수로 Set<AnyCancellable> 타입의 변수를 생성해 여러 Subscription을 관리한다.
(인스턴스가 유지되는 동안 전역변수는 사라지지 않기 때문)