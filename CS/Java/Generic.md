### 제너릭

Java 5에서 추가.

데이터 타입을 특정한 타입으로 정하지 않고 사용할 때마다 바뀔수 있도록 범용적이고 포괄적으로 지정한다라는 의미.

제네릭 타입 ```<T>```을 가지는 클래스와 인터페이스를 말함.

### 제너릭, 제너릭 타입

제너릭 :  타입을 일반화 하는 프로그래밍 스타일을 나타내는 용어

제네릭 타입 : 이렇게 일반화된 타입을 실제로 사용하는 선언된 타입.

```java
// Test 클래스는 제너릭은 사용한 것.
public class Test<T> {
    public T value;
}

// 여기서 String은 제너릭 타입.
Test<String> test = new Test<>();
```

### 사용이유 및 장점

잘못된 타입을 사용될 수 있는 문제(ClassCastException, UncheckedException)를 컴파일 과정에서 제거할 수 있어 에러를 방지.

또한 타입변환을 할 필요가 없어 프로그램 성능이 향상됨.

```java
 //제네릭을 사용하지 않을경우
ArrayList list = new ArrayList();
list.add("test");
String temp = (String) list.get(0); //타입변환이 필요함        

 //제네릭을 사용할 경우
ArrayList<String> list2 = new ArrayList();
list2.add("test");
temp = list2.get(0); //타입변환이 필요없음
```

### 단점

코드 상 제너릭 타입을 알기 어려움.

```java
 //제네릭을 사용하지 않을경우
ArrayList list = new ArrayList();
list.add("test"); // 이때 타입이 정해지므로 위 코드만 봐서는 어떤 타입인지 알 수 없음
String temp = (String) list.get(0); //타입변환이 필요함        

 //제네릭을 사용할 경우 : 타입이 String으로 명확.
ArrayList<String> list2 = new ArrayList();
list2.add("test");
temp = list2.get(0); //타입변환이 필요없음
```

제너릭 배열 생성 불가.

```java
public class Test<T> {
	T[] arr; // 선언 자체는 가능

	// 불가. 오류 발생. : Cannot create a generic array of T
	public Test() {
		arr = new T[10];
	}

		// 이렇게는 가능하지만 배열 타입을 안정성을 보장할 수 없음.
    public ReflectTest() {
        arr = (T[]) Array.newInstance(String.class,10);
        arr[0] = (T) "test";
        System.out.println(arr[0]);
    }

}
```

배열을 생성할 때 제너릭 타입(T)의 실제 타입을 알 수 없기 때문.

### 제너릭 타입 사용.

```java
public class Test<T> {}
public interface Test<T> {}
```

위와 같이 타입을 파라미터로 가지는 클래스와 인터페이스를 의미.

```java
// 제너릭 타입
public class ReflectTest<T> {
    T e;
    public T testParameter(T st) {
        return st;
 }

// 실제 사용시 제너릭에 타입을 넣어 사용.
ReflectTest<String> test = new ReflectTest();
System.out.println(test.testParameter("testing"));
```

### 한정적 타입 매개변수(Bounded type)

타입 파라미터들은 바운드(bound) 될 수 있음.

( 바운드 된다 == 제한된다. 즉, 메서드가 받을 수 있는 타입을 제한할 수 있음 )

```java
package test;

public class GenericTest<T extends String> {
    
    public void set(T t){}
    
    public static void main(String[] args) {
        // 오류 발생 : Type parameter 'java.lang.Integer' is not within its bound; should extend 'java.lang.String'
//        GenericTest<Integer> test = new GenericTest<Integer>();
        GenericTest<String> test = new GenericTest<>();

        
        GenericTest test2 = new GenericTest();
//        test.set(1); // 오류 발생.
        test2.set("test");
        
    }
}

```

`<T extends String>` 일 때 T 타입을 String이 아닌 다른 타입을 주면 아래 오류가 발생.

`Type parameter 'java.lang.Integer' is not within its bound; should extend 'java.lang.String'`

extends 받은 서브타입만 들어갈 수 있음.

### 제너릭과 와일드 카드

와일드 카드도 제네릭과 마찬가지로 바운드를 설정할 수 있음.

상한 한정(extends), 하한 한정(super)을 사용할 수 있음.

```java
public static double sum(List<? extends Number> numbers) {
    double result = 0.0;
    for (Number number : numbers) {
        result += number.doubleValue();
    }
    return result;
}

public static void main(String[] args) {
    List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
    List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3, 4.4, 5.5);
		List<String> list = Arrays.asList("1","2","3");

    double sumOfIntegers = sum(integers); // Integer는 Number를 상속하므로 가능
    double sumOfDoubles = sum(doubles);   // Double도 Number를 상속하므로 가능
		double sumOfString = sum(list);       // 오류발생.
}
```

`제너릭`은 클래스나 메서드를 정의할 때 특정 타입을 동적으로 지정할수 있고, 사용 시에 실제 타입을 지정함. ( 즉, 지정하지 않으면 사용할 수 없음)(타입의 안정성 보장.)

`와일드 카드`는 주로 메서드에 사용되며, 불특정 타입으로 사용. (즉, 다양한 타입을 다룰 수 있도록 유연성 보장)

와일드카드에서는 아래와 같이 사용이 가능하지만

```java
package test;

import java.util.Arrays;
import java.util.List;

public class WildCardTest {

    public static double sum(List<? extends Number> numbers) {
        double result = 0.0;
        for (Number number : numbers) {
            result += number.doubleValue();
        }
        return result;
    }

    public static void main(String[] args) {
        List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
        List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3, 4.4, 5.5);

        double sumOfIntegers = sum(integers); // Integer는 Number를 상속하므로 가능
        double sumOfDoubles = sum(doubles);   // Double도 Number를 상속하므로 가능

    }

}

```

제너릭은 불가능.

```java
public class WildCardTest<T> {

    public static double sum(List<T extends Number> numbers) {
        double result = 0.0;
        for (Number number : numbers) { **// 오류 발생. requied한 타입이 T라고 뜸.**
            result += number.doubleValue();
        }
        return result;
    }

    public static void main(String[] args) {
        List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
        List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3, 4.4, 5.5);

        double sumOfIntegers = sum(integers); // Integer는 Number를 상속하므로 가능
        double sumOfDoubles = sum(doubles);   // Double도 Number를 상속하므로 가능

    }

}
```

참고:

https://dev-coco.tistory.com/28