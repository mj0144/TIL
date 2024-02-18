# 8.1 컬렉션 팩토리

[정적 팩터리 메서드 VS 팩터리 메서드](https://www.notion.so/VS-1b8937da5f2d4c288716a47b7fb71112?pvs=21)

- 객체를 생성하기 위해 인터페이스를 정의하지만, 어떤 클래스의 인스턴스를 생성할지에 대한 결정은 서브클래스가 내리도록 하는 패턴
    - 팩토리 메소드란 쉽게 말하면 객체를 생성 반환하는 메서드이다.
- 다시 말해서 팩토리메서드 패턴이란 **하위 클래스에서 팩토리 메서드를 오버라이딩해서 객체를 반환**하게 하는 패턴이다.

```java
List<String> friends = new ArrayList<>();
friends.add("rea");
friends.add("Olivia");
friends.add("Thibaut");
```

```java
// 팩터리 메서드 이용.
List<String> friends2 = Arrays.asList("rea", "Olivia");
friends2.set(0, "rea2"); // 기존 요소를  갱신할 수 있지만
friends2.add("Thibaut"); // 요소 추가는 불가. java.lang.UnsupportedOperationException 오류 발생
```

`UnsupportedOperationException`

: 내부적으로 고정된 크기의 변환할 수 있는 배열로 구현되었기 때문에 발생.

### 8.1.1 리스트 팩토리

`List.of()` 는 기존 요소 갱신(set())도 `UnsupportedOperationException` 오류 발생함.

```java
List<String> friends3 = List.of("rea", "Olivia");
friends3.set(0, "rea2"); // 기존 요소 갱신도 불가. java.lang.UnsupportedOperationException 오류 발생
friends3.add("Thibaut"); // 요소 추가도 불가. java.lang.UnsupportedOperationException 오류 발생
```

이러한 제약이 나쁘지만은 않음.

컬렉션이 의도치 않게 변하는 것을 막을 수 있기 때문.

데이터 처리 형식을 설정하거나, 데이터를 변환할 필요가 없다면 사용하기 간편한 팩토리 메서드를 이용하는 것이 권장됨.

### 8.1.2 집합 팩토리

set으로도 변경 불가한 집합 생성 가능.

단, 중복인 요소가 있으면 `IllegalArgumentException` 오류 발생.

```java
Set<String> frriends4 = Set.of("rea", "Olivia", "Thibaut");
Set<String> frriends5 = Set.of("rea", "Olivia", "Olivia", "Thibaut"); // IllegalArgumentException 오류 발생
```

### 8.1.3 맵 팩토리

자바 9에서는 2가지 방법으로 바꿀 수 없는 맵을 초기화 할 수 있음.

1. `Map.of`

```java
// 10개 이하의 key,value 쌍을 가진 맵을 만들 때 유용
Map<String, Integer> ageOfFriends = Map.of("Raphael", 30, "Olivia", 24, "Thibaut", 26);
System.out.println(ageOfFriends); // {Olivia=24, Thibaut=26, Raphael=30}
```

1. `Map.ofEntries`

```java
// Map.entry는 Map.Entry객체를 만드는 새로운 팩터리 메서드임.
Map<String, Integer> ageOfFriends2 = Map.ofEntries(entry("Raphael", 30),
        entry("Olivai", 25),
        entry("Thibaut", 26));
System.out.println(ageOfFriends2); // {Raphael=30, Olivai=25, Thibaut=26}
```

# 8.2 리스트와 집합 처리

자바8에서는 List,Set 인터페이스에 다음과 같은 메서드를 추가함.

1. removeIf : 조건에 만족하는 요소를 제거.
2. replaceAll : 리스트에서 이용할 수 있는 기능으로 UnaryOperator함수를 이용해 요소를 바꿈
3. sort : List 인터페이스에서 제공하는 기능으로 리스트를 정렬.

위 메서드들은 호출한 컬렉션 자체를 바꿈. ( 새로 만드는 것이 아닌 기존 컬렉션을 바꾸는것. )

### 8.2.1 removeIf 메서드

아래 코드는 `ConcurrentModificationException` 이 발생함.

```java
for (Transaction transaction : transactions) {
    if(Character.isDigit(transaction.getReferenceCode().charAt(0))){
        transactions.remove(transaction);
    }
}
```

위 코드. for-each 루프는 내부적으로 아래와 같이 동작함.

```java
for(Iterator<Transaction> iterator = trasactions.iterator(); iterator.hasNext();) {
    Transaction transaction = iterator.next();
    if(Character.isDigit(transaction.getReferenceCode().charAt(0))){
        transactions.remove(transaction); // 반복하면서 별도의 두 객체를 통해(transactions, iterator) 컬렉션을 바꾸고 있음.
    }
}
```

위 코드는 2개의 개별 객체가 컬렉션을 관리하고 있음.

1. **Iterator 객체**. next(), hasNext()를 이용해 소스를 질의함.
2. **Collection 객체 자체**. remove()를 호출해 요소를 삭제.

결과적으로 반복자의 상태는 컬렉션의 상태와 서로 동기화되지 않음.

Iterator 객체를 명시적으로 사용하고, 그 객체의 remove() 메서드를 호출함으로 이 문제를 해결할 수 있음.

```java
for(Iterator<Transaction> iterator = trasactions.iterator(); iterator.hasNext();) {
    Transaction transaction = iterator.next();
    if(Character.isDigit(transaction.getReferenceCode().charAt(0))){
        **iterator.remove();** 
    }
}
```

위 코드 패턴은 자바 8의 `removeIf`메서드로 바꿀 수 있음.

removeIf는 삭제할 요소를 가리키는 프레디케이트를 인수로 받음.

```java
transactions.removeIf(transaction 
			-> Character.isDigit(transaction.getReferenceCode().charAt(0)));
```

### 8.2.2 replaceAll 메서드

reaplceAll 메서드를 이용해 리스트의 각 요소를 새로운 요소로 바꿀 수 있음.

아래 코드는 새 문자열 컬렉션을 만듦.

```java
List<String> referenceCodes = List.of("a12", "C14", "b13");
referenceCodes.stream()
        .map(code -> Character.toUpperCase(code.charAt(0)) + code.substring(1))
        .collect(Collectors.toList())
        .forEach(System.out::println); // A12 C14 B13
```

새 문자열 컬렉션을 만드는 것이 아닌 기존 컬렉션을 바꾸기 위해선 아래와 같이 ListIterator 객체 사용.

```java
for (ListIterator<String> iterator = referenceCodes.listIterator(); iterator.hasNext();) {
    String code = iterator.next();
    iterator.set(Character.toUpperCase(code.charAt(0)) + code.substring(1));
}
```

단, 이전과 같이 컬렉션 객체를 Iterator 객체와 혼용하면 반복과 컬렉션 변경이 동시에 이뤄지면서 쉽게 문제를 이르킴.

자바8의 replaceAll을 사용하면 아래와 같이 구현 가능.

```java
referenceCodes.replaceAll(code -> Character.toUpperCase(code.charAt(0)) + code.substring(1));
```

# 8.3 맵 처리

자바 8에서 Map 인터페이스에 몇가지 디폴트 메서드가 추가됨.

### 8.3.1 forEach 메서드

```java
for (Map.Entry<String, Integer> entry : ageOfFriends.entrySet()) {
    String friend = entry.getKey();
    Integer age = entry.getValue();
    System.out.println("friend : " + friend + "age = " + age);
}
```

```java
ageOfFriends.**forEach**((friend, age) -> System.out.println("friend : " + friend + ", age = " + age));
```

### 8.3.2 정렬 메서드

2개의 새로운 유틸리티 사용시 맵의 항목을 값 또는 키를 기준으로 정렬할 수 있음.

`Entry.comparingByValue`

`Entry.comparingByKey`

```java
Map<String, String> favoriteMovies = Map.ofEntries(
																							entry("raphael", "star"), 
																							entry("Cristina", "Matrix"),
																							entry("Oliva", "James bond")
																					);

// 사람의 이름ㅇ르 알파벳 순으로 스트림 요소를 처리함.
//        Cristina=Matrix
//        Oliva=James bond
//        raphael=star
favoriteMovies.entrySet()
        .stream()
        .sorted(Map.Entry.comparingByKey())
        .forEachOrdered(System.out::println);
```

### 8.3.3 getOrDefault메서드

`getOrDefault` : 찾으려는 키가 존재하지 않으면 기본값 반환 메서드

첫번째 인수 : 키

두번째 인수 : 기본값.

→ 맵에 키가 존재하지 않으면 두 번째 인수로 받은 기본값을 반환.

```java
Map<String, String> favouriteMovies = Map.ofEntries(entry("Raphael", "Star Wars"), entry("Olivia", "James Bond"));
System.out.println(favouriteMovies.**getOrDefault**("Olivia", "Matrix")); //James Bond
System.out.println(favouriteMovies.**getOrDefault**("Thibaut", "Matrix")); //Matrix
```

### 8.3.4 계산 패턴

맵에 키가 존재하는지 여부에 따라 어떤 동작을 실행하고 결과를 저장해야 하는 상황

예를들어) 키를 이용해 값비싼 동작을 실행해서 얻은 결과를 캐싱한다면, 키가 존재하면 결과를 다시 계산할 필요 없음.

1. `computeIfAbsent` : 제공된 키에 해당하는 값이 없으면, 키를 이용해 새 값을 계산하고 맵에 추가함.
2. `computeOfPresent`: 제공된 키가 존재하면 새 값을 계산하고 맵에 추가함.
3. `compute`: 제공된 키로 새 값을 계산하고 맵에 저장.

```java
Map<String, List> friendsToMovies = new HashMap<>();
String friend = "Raphael";
List<String> movies = friendsToMovies.get(friend);
if (movies == null) {
    movies = new ArrayList<>();
    friendsToMovies.put(friend, movies);
}
movies.add("Star Wars");
System.out.println(friendsToMovies); // {Raphael=[Star Wars]}
```

키가 존재하지 않으면 값을 계산 맵에 추가하고 키가 존재하면 기존 값을 반환함.

```java
friendsToMovies.computeIfAbsent("Raphael", name -> new ArrayList<>())
        .add("Star Wars");
System.out.println(friendsToMovies); //{Raphael=[Star Wars]}
```

### 8.3.5 삭제패턴

자바8에서는 키가 특정한 값과 연관되었을 때만 항목을 제거하는 remove의 오버로드 버전 메서드를 제공함.

```java
String key = "Raphael";
String value = "Jack Reacher 2";
if (favouriteMovies.containsKey(key) && Objects.equals(favouriteMovies.get(key), value)) {
	  favoriteMovies.remove(key);
	  return true;
} else {
	  return false;
}
```

```java
String key = "Raphael";
String value = "Jack Reacher 2";

favoriteMovies.remove(key, value);
```

### 8.3.6 교체 패턴

맵의 항목을 바꾸는 데 사용할 수 있는 2개 메서드가 추가됨.

1. `reaplceAll`: BiFunction을 적용한 결과로 각 항목의 값을 교체함. 이 메서드는 이전에 살펴본 List의 replaceAll과 비슷한 동작을 수행함.
2. `Replace`: 키가 존재하면 맵의 값을 바꿈. 키가 특정 값으로 매핑되었을 때만 값을 교체하는 오버로드 버전도 있음.

```java
Map<String, String> favouriteMovies2 = new HashMap<>();
favouriteMovies2.put("Raphael", "Star Wars");
favouriteMovies2.put("Olivia", "james bond");
favouriteMovies2.replaceAll((friends22, movie) -> movie.toUpperCase());
System.out.println(favouriteMovies2); //{Olivia=JAMES BOND, Raphael=STAR WARS}
```

### 8.3.7 합침

일반적인 putAll 을 사용하여 Map을 합침

```java
Map<String, String> family = Map.ofEntries(
        entry("Teo", "Star Wars")
        ,entry("Cristina", "James Bond")
);

Map<String, String> friendsMap = Map.ofEntries(
        entry("Raphael", "Star Wars")
);

Map<String, String> everyone = new HashMap<>(family);
everyone.**putAll**(friendsMap);
System.out.println(everyone); // {Cristina=James Bond, Raphael=Star Wars, Teo=Star Wars}
```

만일 같은 key가 있는 경우 충돌을 방지하기 위해 forEach와 merge 메서드를 활용.

```java
Map<String, String> everyone = new HashMap<>(family);
friendsMap.**forEach**((k, v) ->
        everyone.**merge**(k, v, (movie1, movie2) -> movie1 + " & " + movie2));
System.out.println(everyone); //{Raphael=Star Wars, Cristina=James Bond & James Bond22, Teo=Star Wars}
```

merge 메서드는 지정된 키와 연괄된 값이 없거나, 값이 null이면 merget는 키를 null이 아닐 값과 연결함.

아니면 merge는 연결된 값을 주어진 매핑 함수의 결과값으로 대치하거나 결과가 null이면 항목을 제거함.

merge를 사용해 초기화 검사를 구현할 수도 있음.

영화를 몇 회 시청했는지 기록하는 맵이 있을 때, 해당 값을 증가시킥 전에 관련 영화가 이미 맵에 존재하는지 확인.

```java
Map<String, Long> moviesCount = new HashMap<>();
String movieName = "JamesBond";
Long count = moviesCount.get(movieName);
if (count == null) {
    moviesCount.put(movieName, 1L);
} else {
    moviesCount.put(movieName, count + 1);
}
```

```java
moviesCount.merge(movieName, 1L, (count, incremnet) -> count + 1L);
```

# 8.4 개선된 ConcurrentHashMap

ConcurrentHashMap 클래스는 동시성 친화적이며, 최신 기술을 반영한 HashMap버전임.

ConcurrentHashMap은 내부 자료구조의 특정 부분만 잠궈 동시 추가. 갱신 작업을 허용함.

따라서 동기화된 Hashtable 버전에 비해 읽기 쓰기 연산 성능이 월등함. ( HashMap은 비동기)

### 8.4.1 리듀스와 검색

COncurrnetHashMap은 스트림에서 봤던 것과 비슷한 종류의 3가지 새로운 연산을 지원함.

1. `forEach` : 각 (키, 값) 쌍에 주어진액션을 실행
2. `reduce` : 모든 (키, 값) 쌍을 제공된 리듀스 함수를 이용해 결과를 합침.
3. `search` : 널이 아닌 값을 반환할 떄까지 각 ( 키, 값) 쌍에 함수를 적용.

또한 키에 함수받기, 값, Map.Entry, (키,값) 인수를 이용한 4가지 연산 형태를 지원함.

1. 키, 값으로 연산( forEach, reduce, search )
2. 키로 연산 ( forEachKey, reduceKeys, searchKeys )
3. 값으로 연산 ( forEachValue, reduceValues, searchValues )
4. Map.Entry 객체로 연산( forEachEntry, reduceEntries, searchEntries )

위 연산은 ConcurrentHashMap 상태를 잠그지 않고 연산을 수행함.

따라서 이들 연산에 제공한 함수는 계산이 진행되는 동안 바뀔 수 있는 **객체, 값, 순서** 등에 의존해서는 안됨.

또한 이들 연산에 병렬성 기준값(threshold)을 지정해야 함.

맵의 크기가 주어진 기준값보다 작으면 순차적으로 연산을 실행함.

**기준값을 1로 지정하면 공통 스레드 풀을 이용해 병렬성을 극대화 함.**

Long.MAX_VALUE를 기준값으로 설정하면 한 개의 스레드로 연산을 실행함.

만일 사용중인 소프트웨어 아키텍처가 고급 수준의 자원 활용 최적화를 사용하고 있지 않다면 기준값 규칙을 따르는 것이 좋음.

```java
ConcurrentHashMap<String, Long> map = new ConcurrentHashMap<>();
map.put("one", 1L);
map.put("one2", 2L);
map.put("one3", 3L);
map.put("one4", 4L);
long parallelismThreashold = 1;
Optional<Long> maxValue = Optional.ofNullable(map.reduceValues(parallelismThreashold, Long::max));
System.out.println(maxValue); // Optional[1]
```

[ConcurrentHashMap](https://www.notion.so/ConcurrentHashMap-256eaa50165540a8b1a632cd749fd01c?pvs=21)

### 8.4.2 계수

`mappingCount` : 맵의 매핑 개수를 반환하는 ConcurrentHashMap 클래스가 제공하는 메서드.

기존의 size 메서드 대신 새코드에서는 long을 반환하는 mappingCount 메서드를 사용하는 것이 좋음. ( int 범위 넘어서는 상황 대처 가능 )

### 8.4.3 집합뷰

keySet : ConcurrnetHashMap을 집합뷰로 반환.

맵을 바꾸면 집합도 바뀌고 반대로 집합을 바꾸면 맵도 영향을 받음.

newKeySet : ConcurrentHashMap으로 유지되는 집합 만듦.