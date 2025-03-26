
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
