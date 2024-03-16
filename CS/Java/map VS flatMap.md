### **map 메소드**

**`map`** 메소드는 스트림의 각 요소에 주어진 함수를 적용하고 함수의 결과로 구성된 새로운 스트림을 생성.

→ 이 메소드는 한 요소를 다른 요소로 변환하는데 사용됨. 변환 함수는 각 입력 요소에 대해 정확히 하나의 출력 요소를 생성합니다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4);
List<Integer> squaredNumbers = numbers.stream()
                                       .map(n -> n * n) // 제곱처리
                                       .collect(Collectors.toList()); //[1, 4, 9, 16]
```

### **flatMap 메소드**

**`flatMap`** 메소드는 각 입력 요소를 스트림으로 변환하고, 생성된 모든 스트림을 하나의 스트림으로 "평탄화"함.

이는 중첩된 컬렉션 구조를 단일 스트림으로 평탄화하고 싶을 때 유용합니다. 각 입력 요소에 대해 여러 출력 요소가 생성될 수 있습니다.

```java
List<List<Integer>> listOfLists = Arrays.asList(
  Arrays.asList(1, 2),
  Arrays.asList(3, 4),
  Arrays.asList(5, 6)
);
List<Integer> flatList = listOfLists.stream()
                                     .flatMap(List::stream) // 하나로 합침. -> 1:N변환
                                     .collect(Collectors.toList());
```