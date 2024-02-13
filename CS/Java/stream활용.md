# 5.1 필터링

predicate필터링 방법과 고유 요소만 필터링하는 방법.

### Predicate로 필터링

filter메서드는 predicate(Boolean을 반환하는 함수)를 인수로 받아서 Predicate와 일치하는 모든 요소를 포함하는 스트림을 반환.

```java
List<Dish> vegetarianMenu = menu.stream()
                **.filter(Dish::isVegetarian)** // 채식 요리 여부 확인
                .collect(Collectors.toList());
```

### 고유 요소 필터링

스트림은 고유 요소로 이뤄진 스트림을 반환하는 distinct 메서드 지원.

( 고유 여부는 스트림에서 만든 객체의 hashCode, equals로 결정됨. )

```java
List<Integer> numbers = Arrays.asList(1,2,1,3,3,2,4);
        numbers.stream()
                .filter(i -> i % 2 == 0)
                .distinct()
                .forEach(System.out::println); // 2,4
```

# 5.2 스트림 슬라이싱

자바 9이상 기능.

스트림의 요소를 선택하거나 스킵하는 방법 소개.

### Prediate 를 이용한 슬라이싱

자바 9는 스트림의 요소를 효과적으로 선택할 수 있도록 `takeWhile`, `dropWhile` 두 가지 새로운 메서드를 지원.

`takeWhile`

```java
List<Integer> numbers2 = Arrays.asList(2,1,2,1,3,3,2,4);
        numbers2.stream()
                .filter(i -> i % 2 == 0)
                .forEach(System.out::println); // 2,2,4
```

```java
List<Integer> numbers2 = Arrays.asList(2,1,2,1,3,3,2,4);
        numbers2.stream()
                .takeWhile(i -> i % 2 == 0)
                .forEach(System.out::println); // 2
```

위 코드에서 filter 연산을 이용하면 전체 스트림을 반복하면서 각 요소에 prdicate를 적용하게 됨.

즉, `filter`는 모든 조건에 대해 다 검사를 실행하며, 참인 경우 모든 값이 출력됨. 하지만 `takeWhile`은 조건에 대해 참이 아닐 경우 바로 거기서 멈춰버림.

( 따라서 위 예제에서 takeWhile을 적용하게 되면 리스트에 첫번째 인덱스 값 2가 조건에 만족하기 때문에 거기서 멈추면 2만 출력됨. 만일 리스트의 첫번째 인덱스가 조건에 만족하지 않는 홀수 였다면 바로 멈추기 때문에 어떠한 값도 출력하지 않음 ) 

`DropWhile`

거짓이 되면 그 지점에서 작업을 중단하고 남은 모든 요소 반환함.

아래 예제에서는 두 번째 인덱스(1)가 홀수이므로 여기서 중단하고, 그 이후 요소를 반환함.

```java
List<Integer> numbers2 = Arrays.asList(2,**1,2,1,3,3,2,4**);
        numbers2.stream()
                .dropWhile(i -> i % 2 == 0)
                .forEach(System.out::println); // 1,2,1,3,3,2,4
```

### 스트림 축소

`limit` : 주어진 값 이하의 크기를 갖는 스트림 반환

```java
ist<Integer> numbers = Arrays.asList(1,2,1,3,3,2,4);
        numbers.stream()
                .filter(i -> i % 2 == 0)
                .limit(1)
                .forEach(System.out::println); // 원래는 2,4이지만 limit 떄문에 1출력
```

### 요소 건너뛰기

`skip` : 조건에 만족하는  n개 요소를 제외한 스트림을 반환

```java
List<Integer> numbers = Arrays.asList(1,2,1,3,3,2,4);
        numbers.stream()
                .filter(i -> i % 2 == 0)
                .skip(2)
                .forEach(System.out::println); // 원래는 2,4이지만 skip 떄문에 4출력
```

# 5.3 매핑

특정 객체에서 특정 데이터를 선택하는 작업은 데이터 처리 과정에서 자주 수행되는 연삼임.

### 스트림의 각 요소에 함수 적용하기

`map` : 함수를 인수로 받으며, **인수로 제공된 함수는 각 요소에 적용**되며 함수를 적용한 결과가 새로운 요소로 매핑됨.

( 기존의 값을 수정하기 보단 새로운 버전을 만드는 개념임 )

map 함수 인수에 전달된 getName(), length() 함수가 menu각 요소에 적용되어 List로 출력됨.

```java
List<Integer> disNamesLengths = menu.stream()
                .map(Dish::getName)
                .map(String::length)
                .collect(Collectors.toList());
```

### 스트림 평면화

리스트에서 고유문자로 이뤄진 리스트 반환.

아래와 같이 distinct를 사용 할 수 있지만 반환 값이  Stream<String[]>임.

Stream<String> 을 반환하도록 수정해보자

```java
List<String> words = Arrays.asList("Hello", "World");
List<String[]> result = words.stream()
        .map(word -> word.split(""))
        .distinct()
        .collect(Collectors.toList());
```

`flatMap`

```java
List<String> words = Arrays.asList("Hello", "World");
List<String> result = words.stream()
        .map(word -> word.split(""))
        **.flatMap(Arrays::stream)// 생성된 스트림을 하나의 스트림으로 평면화**
        .distinct()
        .collect(Collectors.toList());

for (String s : result) {
    System.out.print(s); //HeloWrd
}
```

flatMap은 각 배열을 스트림이 아닌 스트림의 컨텐츠로 매핑함.

즉, **스트림의 각 값을 다른 스트림으로 만든 다음 모든 스트림을 하나의 스트림으로 연결하는 기능을 함.**

Q. 두 개의 숫자 리스트가 있을 때 모든 숫자 쌍의 리스트를 반환하시오. 예를 들어 두개의 리스트 [1,2,3]과 [3,4]가 주어지면 [(1,3), (1,4), (2,3), (2,4), (3,3), (3,4)]를 반환해야 함.

A.

```java
List<Integer> number1 = Arrays.asList(1, 2, 3);
List<Integer> number2 = Arrays.asList(3,4);

List<int[]> collect = number1.stream()
        .flatMap(i -> number2.stream().map(j -> new int[]{i, j}))
        .collect(Collectors.toList());
```

# 5.4 검색과 매칭

특정 속성이 데이터 집합이 있는지 여부를 검색하는 데이터 처리도 자주 사용됨.

### predicate가 적어도 한 요소와 일치하는지 확인

`anyMatch`

```java
List<String> arrs = Arrays.asList("AEA","CEAEA", "DEA3", "EEAEAEA2");
boolean b = arrs.stream()
        .anyMatch(i -> i.length() < 4); // true
```

### predicate가 모든 요소와 일치하는지 검사

`allMatch`

스트림의 모든 요소가 일치해야함.

```java
List<String> arrs = Arrays.asList("AEA","CEAEA", "DEA3", "EEAEAEA2");
boolean b = arrs.stream()
        .allMatch(i -> i.length() > 2); // true
```

`noneMatch`

일치하는 요소가 없는지 확인.

```java
List<String> arrs = Arrays.asList("AEA","CEAEA", "DEA3", "EEAEAEA2");
boolean b = arrs.stream()
        .noneMatch(i -> i.length() <= 2); // true

System.out.println("b = " + b);
```

`anyMatch`, `allMatch`, `noneMatch` 세 메서드는 스트림 **쇼트서킷** 기법. 즉, 자바의 &&, || 과 같은 연산을 활용함.

`쇼트서킷` : 표현식에서 하나라도 거짓이라는 결과가 나오면 나머지 표현식의 결과와 상관없이 전체결과도 거짓이 되는 상황

### 요소 탐색

`findAny`

현재 스트림에서 임의의 요소를 1개 반환.

```java
Optional<Dish> dish = menu.stream()
							.filter(Dish::isVegetarian)
							.findAny();
```

### 첫번째 요소 찾기

`findFirst`

```java
List<Integer> numbers3 = Arrays.asList(1,2,3,4,5);
Optional<Integer> first = numbers3.stream()
        .map(n -> n*n)
        .filter(i -> i % 3 == 0)
        .findFirst(); // 9
```

`findFirst` && `findAny` 차이

> 스트림을 직렬로 처리할 때는 둘 다 동일한 요소를 반환하지만, 병렬로 처리할 때 차이가 있음.
findFirst는 여러 요소가 조건에 만족해도 stream의 순서를 고려하여 가장 앞에 있는 요소를 리턴.
findAny는 멀티스레드 환경에서 stream처리할 때 가장 먼저 찾은 요소를 리턴함.
따라서 멀티스레드 환경에서 실행할 때마다 findAny는 반환되는 값이 달라질 수 있음.
> 

# 5.5 리듀싱

특정 질의를 수행하려며 Integer와 같은 결과가 나올 때까지 스트림의 모든 요소를 반복적으로 처리.

이런 질의를 리듀싱 연산(모든 스트림 요소를 처리해서 값으로 도출)이라고 함.

### 요소의 합

`reduce`

스트림의 원소들을 하나씩 소모해가며, 누적계산을 수행하고 결과 값을 리턴.

: 2개의 인수를 가지는 경우

1. 초기값
2. 두 요소를 조합해 새로운 값을 만드는 BinaryOperator<T> ( 적용할 계산 함수 )

```java
int sum = numbers3.stream()
        .reduce(0, (i,j) -> i+j);
System.out.println("sum = " + sum);

또는 

int sum = numbers3.stream()
//                .reduce(0, (i,j) -> i+j);
        .reduce(0, Integer::sum);
System.out.println("sum = " + sum);
```

: 1개의 인수를 가지는 경우

1. 두 요소를 조합해 새로운 값을 만드는 BinaryOperator<T> ( 적용할 계산 함수 )

2개 인수를 가지는 것과 다르게 초기값 세팅을 안함.

스트림에 아무 요소가 없을 때 초기값이 없으므로 합계를 반환할 수 없음. 따라서 합계가 없음을 가르킬 Optional객체로 감싸서 반환.

```java
Optional<Integer> reduce = numbers3.stream()
                .reduce(Integer::sum);
```

`BinaryOperator<T>`

> 같은 타입의 파라미터 2개를 받아 결과값을 리턴하는 함수형 인터페이스(Functional Interface) 임.
주로 람다와 메서드 참조를 위해 사용됨.
> 

### 최댓값과 최솟값

최댓값과 최솟값 찾을 때도 reduce 활용가능.

```java
Optional<Integer> reduceMax = numbers3.stream().reduce(Integer::max);
Optional<Integer> reduceMin = numbers3.stream().reduce(Integer::min);
```

### reduce 메서드의 장점과 병렬화

reduce는 내부반복이 추상화되면서 내부 구현에서 병렬로 reduce를 실행함.

( 반복적인 합계에서는 sum 변수를 공유해야해서 병렬화가 어려움. )

# 5.6 실전연습

### 문제

1. 2011년에 일어난 모든 트랜잭션을 찾아 값을 오름차순으로 정리하시오
2. 거래자가 근무하는 모든 도시를 중복없이 나열하시오
3. 케이브리지 에서 근무하는 모든 거래자를 찾아서 이름순으로 정렬하시오
4. 모든 거래자의 이름을 알파벳순으로 정렬해서 반환하시오
5. 밀라노에 거래자가 있는가?
6. 케임브리지에 거주하는 거래자의 모든 트랜잭션값을 출력하시오
7. 전체 트랜잭션 중 최댓값은 얼마인가?
8. 전체 트랜잭션 중 최솟값은 얼마인가?

### 거래자와 트랜잭션 소스

- 거래자(Trader)
    
    ```java
    package jpabook;
    
    import java.util.Objects;
    
    public class Trader {
    
      private String name;
      private String city;
    
      public Trader(String n, String c) {
        name = n;
        city = c;
      }
    
      public String getName() {
        return name;
      }
    
      public String getCity() {
        return city;
      }
    
      public void setCity(String newCity) {
        city = newCity;
      }
    
      @Override
      public int hashCode() {
        int hash = 17;
        hash = hash * 31 + (name == null ? 0 : name.hashCode());
        hash = hash * 31 + (city == null ? 0 : city.hashCode());
        return hash;
      }
    
      @Override
      public boolean equals(Object other) {
        if (other == this) {
          return true;
        }
        if (!(other instanceof Trader)) {
          return false;
        }
        Trader o = (Trader) other;
        boolean eq = Objects.equals(name,  o.getName());
        eq = eq && Objects.equals(city, o.getCity());
        return eq;
      }
    
      @Override
      public String toString() {
        return String.format("Trader:%s in %s", name, city);
      }
    
    }
    ```
    
- 트랜잭션(Transaction)
    
    ```java
    package jpabook;
    
    import java.util.Objects;
    
    public class Transaction {
    
      private Trader trader;
      private int year;
      private int value;
    
      public Transaction(Trader trader, int year, int value) {
        this.trader = trader;
        this.year = year;
        this.value = value;
      }
    
      public Trader getTrader() {
        return trader;
      }
    
      public int getYear() {
        return year;
      }
    
      public int getValue() {
        return value;
      }
    
      @Override
      public int hashCode() {
        int hash = 17;
        hash = hash * 31 + (trader == null ? 0 : trader.hashCode());
        hash = hash * 31 + year;
        hash = hash * 31 + value;
        return hash;
      }
    
      @Override
      public boolean equals(Object other) {
        if (other == this) {
          return true;
        }
        if (!(other instanceof Transaction)) {
          return false;
        }
        Transaction o = (Transaction) other;
        boolean eq = Objects.equals(trader,  o.getTrader());
        eq = eq && year == o.getYear();
        eq = eq && value == o.getValue();
        return eq;
      }
    
      @SuppressWarnings("boxing")
      @Override
      public String toString() {
        return String.format("{%s, year: %d, value: %d}", trader, year, value);
      }
    
    }
    ```
    
- 객체생성
    
    ```java
    Trader raoul = new Trader("Raoul", "Cambridge");
    Trader mario = new Trader("Mario", "Milan");
    Trader alan = new Trader("Alan", "Cambridge");
    Trader brian = new Trader("Brian", "Cambridge");
    
    List<Transaction> transactions = Arrays.asList(
          new Transaction(brian, 2011, 300),
          new Transaction(raoul, 2012, 1000),
          new Transaction(raoul, 2011, 400),
          new Transaction(mario, 2012, 710),
          new Transaction(mario, 2012, 700),
          new Transaction(alan, 2012, 950)
    );
    ```
    

### 문제풀이

- 1번 풀이
    
    ```java
    List<Transaction> collect = transactions.stream()
                    .filter(transaction -> transaction.getYear() == 2011)
                    .sorted(Comparator.comparing(Transaction::getValue))
                    .collect(Collectors.toList());
    ```
    
- 2번 풀이
    
    ```java
    List<String> collect1 = transactions.stream()
                    .map(transaction -> transaction.getTrader().getCity())
                    .distinct()
                    .collect(Collectors.toList());
    ```
    
- 3번 풀이
    
    ```java
    List<Trader> collect3 = transactions.stream()
                    .map(Transaction::getTrader)
                    .filter(trader -> trader.getCity().equals("Cambridge"))
                    .distinct()
                    .sorted(Comparator.comparing(Trader::getName))
                    .collect(Collectors.toList());
    ```
    
- 4번 풀이
    
    ```java
    String collect4 = transactions.stream()
                    .map(transaction -> transaction.getTrader().getName())
                    .distinct()
                    .sorted()
                    .collect(Collectors.joining());
    ```
    
- 5번 풀이
    
    ```java
    boolean milan = transactions.stream()
                    .map(Transaction::getTrader)
                    .anyMatch(trader -> trader.getCity().equals("Milan"));
    ```
    
- 6번 풀이
    
    ```java
    //케임브리지에 거주하는 거래자의 모든 트랜잭션값을 출력하시오
    transactions.stream()
            .filter(transaction -> transaction.getTrader().getCity().equals("Cambridge"))
            .map(Transaction::getValue)
            .forEach(System.out::println);
    ```
    
- 7번 풀이
    
    ```java
    //전체 트랜잭션 중 최댓값은 얼마인가?
    Optional<Integer> reduce = transactions.stream()
            .map(Transaction::getValue)
            .reduce(Integer::max);
    ```
    
- 8번 풀이
    
    ```java
    //전체 트랜잭션 중 최솟값은 얼마인가?
            Optional<Integer> reduce2 = transactions.stream()
                    .reduce((t1, t2) -> t1.getValue() < t2.getValue() ? t1 : t2));
    //                .map(Transaction::getValue)
    //                .reduce(Integer::min);
    ```
    

# 5.7 숫자형 스트림

### 기본형 특화 스트림

자바 8에서 3가지 기본형 특화 스트림을 제공함.

1. IntStream : int 요소에 특화
2. DoubleStream : double 요소에 특화
3. LongStream : long 요소에 특화

자주 사용하는 숫자 관련 리듀싱 연산 수행 메서드르 제공함 ( sum, max 등 ..)

또한 필요할 떄 다시 객체 스트림으로 복원하는 기능도 제공함.

특화 스트림은 오직 박싱 과정에서 일어나는 효율성과 관련 있으며 스트림에 추가 기능을 제공하지는 않음.

숫자 스트림으로 매핑

스트림을 특화 스트림으로 변환할 때는 mapToInt, mapToDoubel, mapToLong 세가지 메서드를 사용.

```java
int calories = menu.stream()
									.mapToInt(Dish::getCalories)
									.sum();
```

객체 스트림으로 복원하기

```java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
Stream<Integer> stream = intStream.boxed();
```

기본값 : OptionalInt

IntStream 에서 최댓값을 찾을 때는 0이라는 기본값 때문에 잘못된 결과가 도출될 수 있음.

스트림에 요소가 없는 상황과 실제 최대값이 0인 경우를 구별하기 위해 Optional을 사용.

Optional을 Integer, String, 등의 참조 형식으로 파라미터화 할 수 있으며, 기본형 특화 스트림버전인 OptionalInt, OptionalDouble, OptionalLong을 제공함.

```java
OptionalInt maxCalories = menu.stream()
														.mapToInt(Dish::getCalories)
														.max();

int max = maxCalories.orElse(1); // 값이 없을 때 기본 최댓값을 1로 설정
```

### 숫자 범위

프로그램에서 특정 범위의 숫자를 이용해야 하는 상황 발생 가능.

range 메서드는 시작값과 종료값이 결과에 포함되지 않고,

rangeClosed는 시작값과 종료값이 결과에 포함됨.

# 5.8 스트림 만들기

일련의 값, 배열, 파일, 함수를 이용한 무한 스트림 만들기 등 다양한 방식으로 스트림을 만드는 방법.

### 값으로 스트림 만들기

임의의 수를 인수로 받는 정적 메서드 Stream.of를 이용해서 스트림을 만들 수 있음.

```java
Stream<String> stringStream = Stream.of("Modern ", "java ", "In ", "Action");
stringStream.map(String::toUpperCase)
                .forEach(System.out::println);
```

empty로 스트림을 비울 수 있음.

```java
Stream<Object> empty = Stream.empty();
```

### null이 될 수 있는 객체로 스트림 만들기

자바 9에서는 null이 될 수 있는 객체를 스트림으로 만들 수 있는 새로운 메서드가 추가됨.

```java
String homeValue = System.getProperty("home");
Stream<String> homeValueStream = homeValue == null ? Stream.empty() : Stream.of(homeValue);
```

```java
Stream<String> home = Stream.ofNullable(System.getProperty("home"));
```

null이 될 수 있는 객체를 포함하는 스트림 값을 flatMap과 함께 사용 가능.

```java
Stream<String> stringStream1 = Stream.of("config", "home", "user")
        .flatMap(key -> Stream.ofNullable(System.getProperty(key)));
```

### 배열로 스트림 만들기

```java
int[] numbers = {2,3,4,5,7,11,13};
int sum = Arrays.stream(numbers).sum();
```

### 파일로 스트림 만들기

파일을 처리하는 등의 I/O연산에 사용하는 자바의 NIO API(비블록 I/O)도 스트림 API를 활용할 수 있도록 업데이트 됨.

파일에서 고유한 단어 수를 찾는 프로그램

FIles.lines로 파일의 각 행 요소를 반환하는 스트림을 얻을 수 있음.

각 행 단어를 여러 스트림으로 만드는 것이 아닌 flatMap으로 스트림을 하나로 평면화함.

```java
long uniqueWords = 0;
try(Stream<String> lines = Files.lines(Paths.get("data.txt"), Charset.defaultCharset())) {
    uniqueWords = lines.flatMap(line -> Arrays.stream(line.split(" ")))
            .distinct()
            .count();
} catch (IOException e) {
    // 예외발생 처리
}
```

### 함수로 무한 스트림 만들기

스트림 API는 함수에서 스트림을 만들 수 있는 두 정적 메서드 Stream.iterate와 Stream.generate를 제공함.

iterate 메서드

```java
Stream.iterate(0, n -> n+2)
		.limit(10)
		.forEach(System.out::println);
```

자바 9의 iterate메서드는 프리디케이트를 지원함

예를들어 0에서 시작해서 100보다 크면 숫자 생성을 중단하는 코드 구현이 가능함.

```java
IntStream.iterate(0, n -> n< 100, n-> n + 4)
		.forEach(System.out::println);
```

generate 메서드

generate도 무한 스트림을 만들 순 있지만 iterate와 달리 생상된 각 값을 연속적으로 계산하지 않음

```java
Stream.generate(Math::random)
	.limit(5)
	.forEach(System.out::println);
```


내용 출처 : https://www.hanbit.co.kr/store/books/look.php?p_code=B4926602499