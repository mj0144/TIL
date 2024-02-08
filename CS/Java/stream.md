# 4.1 스트림이란

자바 8 API에 새로 추가된 기능.

스트림을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있다.

데이터 처리 연산을 지원하도록소스에서 추출된 연속된 요소.

**일단 스트림은 데이터 컬렉션 반복을 멋지게 처리하는 기능이라 생각하기** 🙂

### 자바 7과 자바 8 (스트림 사용)의 차이

```java
//--------------------------------------------------------------------------------------
//데이터 선언
//--------------------------------------------------------------------------------------

public static class Dish {
    private int calories;
    private String name;
    private boolean vegetarian;
    private Type type;

    public enum Type { MEAT, FISH, OTHER}

    public Dish(int calories, String name, boolean vegetarian, Type type) {
        this.calories = calories;
        this.name = name;
        this.vegetarian = vegetarian;
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getCalories() {
        return calories;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }
}

//--------------------------------------------------------------------------------------
// 로직
//--------------------------------------------------------------------------------------

List<Dish> menu = Arrays.asList(
        new Dish(800, "pork", false, Dish.Type.MEAT ),
        new Dish(700, "beef", false, Dish.Type.MEAT ),
        new Dish(400, "chicken", false, Dish.Type.MEAT ),
        new Dish(530, "french fries", true, Dish.Type.OTHER ),
        new Dish(350, "rice", true, Dish.Type.OTHER ),
        new Dish(120, "season fruit", true, Dish.Type.OTHER ),
        new Dish(450, "salmon", false, Dish.Type.FISH )
);

List<Dish> lowCaloricDishees = new ArrayList<>();

// 400 칼로리 이하 요리 선택.
for(Dish dish : menu) {
    if(dish.getCalories() < 400) {
        lowCaloricDishees.add(dish);
    }
}

// 요리 정렬
Collections.sort(lowCaloricDishees, new Comparator<Dish>() {
    @Override
    public int compare(Dish o1, Dish o2) {
        return Integer.compare(o1.getCalories(), o2.getCalories());
    }
});

// 정렬된 요리 이름 선택
List<String> lowCaloriDishesName = new ArrayList<>();
for(Dish dish : lowCaloricDishees) {
    lowCaloriDishesName.add(dish.getName());
}
```

```java
List<String> lowCaloriDishesName =
        menu.stream()
                .filter(d -> d.getCalories() < 400) // 400칼로리 이하의 요리 선택
                .sorted(Comparator.comparing(Dish::getCalories)) // 칼로리로 요리를 정렬
                .map(Dish::getName)// 요리명을 추출
                .collect(Collectors.toList()); // 모든 요리명을 리스트에 저장

// parallelStream : 병렬로 실행.
List<String> lowCaloriDishesNameParallel =
        menu.parallelStream()
                .filter(d -> d.getCalories() < 400) // 400칼로리 이하의 요리 선택
                .sorted(Comparator.comparing(Dish::getCalories)) // 칼로리로 요리를 정렬
                .map(Dish::getName)// 요리명을 추출
                .collect(Collectors.toList()); // 모든 요리명을 리스트에 저장
```

### 스트림 장점

1. 선언형으로 코드를 구현할 수 있음.
    
    → 자바 7버전과 같이 루프와 if 조건문등의 제어 블럭을 사용해서 어떻게 동작을 구현할지 지정할 필요없이. ‘저칼로리의 요리만 선택하라’ 라는 동작의 수행을 지정할 수 있음.
    
2. filter, sorted, map, collect 같은 여러 빌딩 블럭 연산을 연결해서 복잡한 데이터 처리 파이프라인을 만들 수 있음. → 조립이 가능하며 유연성이 좋아짐.
3. 병렬화로 인해 성능이 좋아짐.

# 4.2 스트림 시작하기

### 스트림이란.

**`데이터 처리 연산`**을 지원하도록 **`소스`**에서 추출된 **`연속된 요소`**.

`연속된 요소`

스트림은 특정 요소 형식으로 이뤄진 연속된 값 집합의 인터페이스를 제공함.

**컬렉션** : 자료구조이므로 ArrayList나 LinkedList 등을 사용할 것인지에 대한 시간과 공간의 복잡성과 관련된 요소 저장 및 접근 연산이 주를 이룸.

**스트림** : filter, sorterd, map 처럼 표현 계산식을 주를 이룸.

 → 즉, 컬렉션의 주제는 데이터이고 스트림의 주제는 계산임.

 `소스`

스트림은 컬렉션, 배역, I/O자원등의 데이터 제공 소스로부터 데이터를 소비함.

즉, 리스트로 스트림을 만들면 스트림의 요소는 리스트의 요소와 같은 순서를 유지함.

`데이터 처리 연산`

filter, map, reduce,find, match, sort등으로 데이터로 조작할 수 있으며, 스트림 연산은 순차적으로 또는 병렬로 실행할 수 있음.

### 스트림의 주요 특징 2가지

1. 파이프라이닝(Pipelining)
    
    대부분의 스트림 연산은 스트림 연산끼리 연결해서 커다란 파이프라인을 구성할 수 있도록 스트림 자신을 반환함.
    
    → 이 덕분에 게으름(Laziness), 쇼트서킷(short-circuiting)과 같은 최적화도 얻을 수 있음 ( 5장에서 설명)
    
    연산 파이프라인은 데이터 소스에 적용하는 데이터베이스 질의와 비슷함.
    
2. 내부 반복
    
    반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 배부반복을 지원함. 
    

### 예제 기반 추가 설명

```java
List<String> lowCaloriDishesName =
    menu.stream() // 메뉴에서 스트림을 얻음
            .filter(d -> d.getCalories() < 400) // 파이프라인 연산만들기. 첫번쨰로 400이하 필터.
            .sorted(Comparator.comparing(Dish::getCalories)) // 칼로리로 요리를 정렬
            .map(Dish::getName)// 요리명을 추출
						.limit(3) // 선착순 3개만 선택
            .collect(Collectors.toList()); // 결과를 다른 리스트로 저장
```

여기서 `데이터 소스`는 스트림을 얻은 menu임.

`데이터 소스`는 `연속된 요소`를 스트림에 제공함.

다음으로 스트림에 filter, map, limit, collect로 이어지는 일련의 `데이터 처리연산`을 적용함.

**파이프라인은 소스에 적용되는 질의 같은 존재.**

마지막으로 collect연산으로 파이프라인을 처리해서 결과를 반환함.

( 마지막 collect를 호출하리 전까지는 menu에서 무엇도 선택되지 않으며, 출력 결과도 없음 )

**collect** : 스트림을 다른 형식으로 변환.

# 4.3 스트림과 컬렉션

데이터를 언제 계산하느냐가 컬렉션과 스트림의 가장 큰 차이.

컬렉션은 현재 자료구조가 포함하는 모든 값을 메모링 저장하는 자료구조임.

( 즉, 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산됨 )

스트림은 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조임.

( 따라서 스트림에 요소를 추가하거나 스트림에서 요소를 제거할 수 없음 )

### 딱 한 번만 탐색.

스트림도 한 번만 탐색할 수 있음. 즉. 탐색된 스트림의 요소는 소비됨.

→ 따라서 한 번 탐색한 요소를 다시 탐색하려면 초기 데이터 소스에서 새로운 스트림을 만들어야 함.

### 외부반복과 내부반복

외부반복(External Iteration) : 컬렉션 인터페이스를 사용하려면 사용자가 직접 요소를 반복해야함 ( for-each 와 같이 )

내부반복(Internal Iteration) : 스트림 라이브러리는 반복을 알아서 처리하고 결과 스트림값을 어딘가에 저장함.

( 즉, 함수에 어떤 작업을 수행 할 지만 지정하면 모든 것이 알아서 처리됨 )

# 4.4 스트림 연산

스트림 인터페이스 연산은 크게 2가지로 구분가능

1. `중간연산(Intermediate Operation)` : 연결할 수 있는 스트림 연산
2. `최종연산(Terminal Operation)` : 스트림을 닫는 연산

```java
List<String> lowCaloriDishesName =
    menu.stream() // 메뉴에서 스트림을 얻음
            .filter(d -> d.getCalories() < 400) // 중간연산
            .sorted(Comparator.comparing(Dish::getCalories)) // 중간연산
            .map(Dish::getName) // 중간연산
            .collect(Collectors.toList()); // 최종연산
```

### 중간연산

**filter나 sorted 같은 중간연산은 다른 스트림을 반환함.**

따라서 여러 중간 연산을 연결해서 질의를 만들 수 있음.

중간 연산의 중요한 특징은 **단말 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않음**. → 즉 게으르다(Lazy)는 것.

→ **이는 중간연산을 합친 다음에 합쳐진 중간연산을 최종연산으로 한 번에 처리하기 때문**

### 최종연산

최종연산은 **스트림 파이프라인에서 결과를 도출함.**

보통 최종연산에 의해 List, Integer, void 등 **스트림 이외의 결과가 반환됨.**

### 스트림 이용하기.

스트림 이용과정은 3가지.

1. 정의를 수행할 ( 컬렉션 같은 ) 데이터 소스
2. 스트림 파이프라인을 구성할 중간 연산 연결
3. 스트림 파이프라인을 실행하고 결과를 만들 최종연산