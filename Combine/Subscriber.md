
# Subscriber
> **Subscriber**는 **Publisher**로부터 데이터를 수신하는 객체.
> 
> Publisher는 데이터를 생성하고 이벤트 스트림을 방출한다.
> 
> Subscriber는 Publisher의 이벤트를 구독(Subscribe) 하고, 데이터를 받아 처리한다.
>
>Publisher가 데이터를 방출하면, Subscriber는 이를 받아 가공하거나 UI 업데이트 등의 작업을 수행한다.

## sink

### **sink(receiveValue:)**
 
### **sink(receiveCompletion: receiveValue)**

-> Failure의 여부에 따라 두 메소드를 사용할 수 있는지, sink(receiveCompletion: receiveValue)만 사용할 수 있는지로 나뉜다.

## assign
-> 보통 sink를 범용적으로 많이 사용한다.
-> 특정 변수에 값을 할당하는 용도로만 사용할 경우 assign을 고려해봐도 좋다.

### **assign(to:on:)**
```swift
import Combine

class CombineExample {
    var value : String = "" {
        didSet {
            print("\(oldValue) -> \(value)")
        }
    }

    var cancellables = Set<AnyCancellable>()

    init()
    {
        Timer.publish(every: 1.0, on : .main, in : .common)
        .autoconnect()
        .map { date in 
            let formatter = DateFormatter()
            formatter.dateFormat = "HH:mm:ss"
            return formatter.string(from: date)
        }
        .assign(to: \.value, on: self)
        .store(in: &cancellables)
    }

    deinit {
        print("deinit")
    }
}

func excute()
{
    let exam = CombineExample()
    print("excute 종료")
}

excute()
```
-> 이 방법(assign(to:on:))의 경우 순환 참조가 일어나 메모리 누수 문제를 발생시킬 위험이 있다.
-> 이 연산자에 의해 생성된 인스턴스는 파라미터로 전달한 객체에 대한 강한 참조를 유지한다.
-> 위 코드를 예시로 들어보면, excute 함수에서 인스턴스를 생성하고, 초기화 과정에서 assign 연산자를 실행하는데
해당 인스턴스는 cancellable을 프로퍼티로 갖고 있고, cancellable은 인스턴스를 self로 참조하고 있다.
함수가 실행되고, 종료됨에 따라 해당 인스턴스는 메모리에서 해제가 되어야 하지만, 인스턴스 내부 cancellables에서 인스턴스를 참조하고 있어
인스턴스의 참조 카운트가 감소되지 않는다.
또한, cancellables 프로퍼티도 위와 같은 상황에서 속해있는 인스턴스가 메모리에 아직 존재해 참조 카운트가 감소하지 않는다.
즉, 서로가 서로를 참조해 참조 카운트가 내려가질 않아 순환 참조가 발생된다.
-> 이런 순환참조가 일어나지 않게 구현하려면, assign을 사용하지 않고, sink를 사용해 weak, unowned를 사용해 약한 참조 처리한다.
-> 또는, assign(to:)를 사용한다. 단, @Published 프로퍼티 래퍼를 사용해 프로퍼티를 구현하고, 퍼블리셔를 바인딩해 사용한다..
-> assign(to:)의 경우 store(in:)을 사용하지 않아도 된다. @Published 래퍼를 통해 값을 재발행.
-> assign은 내부에서 자동으로 메모리 관리를 함. 위 순환참조 내용과 연관되는 내용으로 생각됨.

### **assign(to:)**
```swift
import Combine

class CombineExample {
    @Published var value : String = "" {
        didSet {
            print("\(oldValue) -> \(value)")
        }
    }

    var cancellables = Set<AnyCancellable>()

    init()
    {
        Timer.publish(every: 1.0, on : .main, in : .common)
        .autoconnect()
        .map { date in 
            let formatter = DateFormatter()
            formatter.dateFormat = "HH:mm:ss"
            return formatter.string(from: date)
        }
        .assign(to: &self.$value)

    }
}

```
