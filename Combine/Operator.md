
# Operator
- Operator는 Publisher가 방출하는 값에 변형, 필터링, 결합 등의 작업을 수행하는 메서드다. 데이터 스트림을 가공하고 조작하는 역할을 함.

## Basic Operator

### map
![map](map_operator.png)
![map result](map_result.png)
- 클로저 내부에 구현한 연산을 진행하고, 그 결과를 기반으로 Publisher를 리턴한다.
 
### filter
- 클로저에 true/false를 판단하는 연산을 진행하고, true를 만족하는 값으로 구성된 Publisher를 리턴한다.

### reduce

### scan

### compactMap

### replaceNil