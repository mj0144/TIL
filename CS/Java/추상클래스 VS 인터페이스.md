## 추상클래스

클래스를 설계도에 비유한다면, 추상클래스는 **미완성 설계도**에 비유할 수 있다. 단어 뜻 그대로 완성되지 못한 채로 남겨진 설계도를 말한다.

**미완성 메서드(추상메서드)를 포함하고 있다는 의미**이다. 추상메서드를 포함하고 있다는 것을 제외하고는 일반 클래스와 전혀 다르지 않다.

추상클래스는 그 자체로 클래스 역할을 다 하지 못하지만, 새로운 클래스를 작성하는데 있어서 바탕이 되는 조상클래스로서의 중요한 역할을 한다.

## 추상메서드(abstract Method)

메서드는 선언부와 구현부로 구성되어 있다.

추상메서드란 선언부만 작성하고 **구현부는 작성하지 않은 채로 남겨 둔 것**이 추상메서드이다,

실제 내용은 상속받는 클래스에서 구현하도록 비워둔 것이다. 추상메서드 역시 키워드 **'abstract'**를 앞에 붙여주고, 구현부가 위치할 { }대신 마침을 의미하는 ; 을 작성한다.

***!만일, 조상으로부터 상속받은 추상메서드 중 하나라도 구현하지 않는다면, 추상클래스로 지정해주어야 한다.***



## 왜 추상클래스를 사용하는가?

자손 클래스에서 추상메서드를 반드시 구현하도록 강요하기 위해서이다.

상속받는 자손클래스에서는 메서드들이 완전히 구현된 것으로 인식하고

오버라이딩을 하지 않을 수 있기 때문이다.

**맥락에 따라서 달라질 수 있는 기능들이 있을 때, 추상 메소드로 만든다.**

추상 클래스로 정의를 한 다음에,,

**달라질 가능성이 없는 부분에 대해서는 일반 메소드로 작성을 하고**

**달라질 가능성이 존재하는 메소드에 대해서는 추상 메소드로 정의를 해준다.**

이렇게 되면 사용자의 입장에서 추상 메소드에 대한 부분을 직접 정의하여

상황에 맞게 클래스를 사용할 수 있다.

이 또한 코드의 중복을 막기 위한 일환이라고 볼 수 있다.

자바는 객체지향 언어이고, 탄생의 목적으로 모든 문법이 귀결된다고 볼 수 있다.

탄생 목적은 코드 재사용성을 높이자. 즉 '중복되는 코드를 없애자'인 것이다.

이번 abstract 문법도 그 목적의 일환으로서 이해를 한다면 좀 더 쉽게 다가올 수 있다.)

[[JAVA] 7. 추상클래스와 추상메서드](https://asfirstalways.tistory.com/165)

---

추상 클래스는 하나 이상의 추상 메서드를 포함할 수 있는 클래스로, 객체를 직접 생성할 수 없습니다. 

추상 클래스는 일반 메서드뿐만 아니라 추상 메서드도 포함할 수 있습니다. 

추상 메서드는 선언만 되어 있고 실제 구현은 하위 클래스에서 이루어져야 합니다.

추상 클래스는 **`abstract`** 키워드를 사용하여 선언하며, 추상 메서드를 가질 수 있습니다. 추상 메서드를 가진 클래스는 반드시 자체적으로 객체를 생성할 수 없으며, 이 클래스를 상속받는 하위 클래스에서 추상 메서드를 구현해야 합니다.

추상 클래스의 특징:

1. **추상 메서드:** 추상 클래스는 추상 메서드를 가질 수 있습니다. 추상 메서드는 선언만 되어 있고 구현은 하위 클래스에서 이루어져야 합니다.
2. **일반 메서드:** 추상 클래스는 일반적인 메서드도 가질 수 있습니다. 이 메서드들은 구현이 제공되어 있습니다.
3. **객체 생성 불가:** 추상 클래스는 직접 객체를 생성할 수 없습니다. 추상 클래스를 사용하기 위해서는 해당 클래스를 상속받는 하위 클래스를 만들고, 추상 메서드를 구현해야 합니다.
4. **상속과 다형성:** 추상 클래스는 상속을 통해 확장될 수 있으며, 다형성을 제공합니다.

간단한 예제를 통해 추상 클래스를 이해해봅시다:

```java
// 추상클래스 Shape
abstract class Shape {
    private String color;

    public Shape(String color) {
        this.color = color;
    }

    // 추상 메서드
    public **abstract** double calculateArea();

    // 일반 메서드
    public void displayColor() {
        System.out.println("Color: " + color);
    }
}

// 자손 클래스1
class Circle extends Shape {
    private double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    // 추상 메서드 구현
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }

		// 일반메서드는 재정의 하지 않아도됨.
}

// 자손 클래스2
class Rectangle extends Shape {
    private double length;
    private double width;

    public Rectangle(String color, double length, double width) {
        super(color);
        this.length = length;
        this.width = width;
    }

    // 추상 메서드 구현
    @Override
    public double calculateArea() {
        return length * width;
    }

// 일반메서드는 재정의 하지 않아도됨.
}

public class Main {
    public static void main(String[] args) {
        Circle circle = new Circle("Red", 5.0);
        circle.displayColor();
        System.out.println("Area: " + circle.calculateArea());

        Rectangle rectangle = new Rectangle("Blue", 4.0, 6.0);
        rectangle.displayColor();
        System.out.println("Area: " + rectangle.calculateArea());
    }
}
```

---

### 자바8 이후 인터페이스

자바 8 이후에 인터페이스에 디폴트 메서드가 생겼다.

디폴트 메서드가 생기면서 추상 메서드의 존재의의에 대해 궁금해..!

이전에는 추상메서드가 추상메서드를 가지고 있어, 달라진 가능성에 있는 메서드에 한해서는 추상 메서드로 선언하여 자손 클래스가 강제로 구현하지 않아도 되는 장점이 있으나,

인터페이스에 디폴트 메서드가 생기면서 추상클래스가 필요 없어지지 않았나,,.!

---

### 인터페이스와 추상클래스 차이

그래도 추상클래스와는 차이가 있다.

1. 추상클래스는 필드(맴버변수)를 가질 수 있으나, 인터페이스는 불가하다.
    
    단, public static, private static, final 변수는 가능. ( 단, private 이기 때문에 인터페이스 내에서만 사용가능 )
    
    ```java
    // 상수 필드 (public, static, final 특성 가짐)
    int MAX_COUNT = 100;
    
    // Java 9부터 허용되는 private 상수 필드
    private String SECRET_KEY = "123456";
    
    // Java 9부터 허용되는 private static 변수
    private static int instanceCount = 0;
    ```
    
2. 또한 원래 인터페이스는 private 메서드를 가질 수 없지만 Java 9부터는 가능해! 
    
    ```java
    // Java 9부터 허용되는 private 메서드
    private void privateMethod() {
    	System.out.println("Private method in interface");
    }
    ```
    
    → 추상 클래스는 가질 수 없는 필드는 명시적으로 제한된게 없어!
    
3. 추상클래스는 생성자를 가져서 객체를 초기화할 수 있지만 인터페이스는 생성자를 가질 수 없어.
4. 추상클래스는 다중 상속이 불가하지만 인터페이스는 가능하지!

---

### 추상 골격 구현 클래스

**인터페이스를 구현하는데 필요한 기본적인 구현을 제공하는 추상 클래스.**

해당 인터페이스의 모든 메서드를 추상 메서드로 선언하며, 이러한 메서드들에 대한 기본적인 로직이나 공통 동작을 구현함.

성격이 유사한 클래스들의 공통 부분을 묶은 후 인터페이스에서 메소드를 정의하고, 인터페이스를 구현한 추상 클래스인 골격 구현 클래스에서 기본적인 골격을 구현하며, 하위 클래스에서 인터페이스와 골격 구현 클래스를 상속받아 메소드를 오버라이딩하는 방식으로 아키텍쳐를 변경

[Spring Boot Interface - 골격 구현 클래스 - 클래스 구조 변경(Feat. Composition)](https://velog.io/@gale4739/Spring-Boot-Interface-골격-구현-클래스-클래스-구조-변경Feat.-Composition)

---

추상 골격 구현 클래스를 사용하면 인터페이스를 구현하는 클래스가 해당 인터페이스의 모든 메서드를 직접 구현하지 않고도 골격 구현 클래스에서 제공하는 기본 구현을 상속받아 사용할 수 있음.

예를 들어, Java에서는 AbasctractList, AbasctractSet, AbastractMap 과 같은 추상 골격 구현 클래스가 있음.

이러한 클래스들은 각각 List, Set, Map 인터페이스를 구현하는데 필요한 메서드들의 기본 구현을 제공하고, 구현 클래스들은 이를 상속받아 필요한 부분만 구현함.

추상 골격 구현 클래스인 AbstarctList 코드

```java
import java.util.AbstractList;

// AbstractList를 상속받는 구현 클래스
class MyList extends AbstractList<String> {
    private String[] array;  // 실제 데이터를 저장하기 위한 배열

    public MyList(String[] array) {
        this.array = array;
    }

    // List 인터페이스의 size 메서드 구현
    @Override
    public int size() {
        return array.length;
    }

    // List 인터페이스의 get 메서드 구현
    @Override
    public String get(int index) {
        if (index < 0 || index >= array.length) {
            throw new IndexOutOfBoundsException("Invalid index");
        }
        return array[index];
    }

    // List 인터페이스의 set 메서드 구현
    @Override
    public String set(int index, String element) {
        if (index < 0 || index >= array.length) {
            throw new IndexOutOfBoundsException("Invalid index");
        }
        String oldValue = array[index];
        array[index] = element;
        return oldValue;
    }
}

public class Main {
    public static void main(String[] args) {
        String[] data = {"A", "B", "C"};
        MyList myList = new MyList(data);

        System.out.println("Size: " + myList.size());
        System.out.println("Element at index 1: " + myList.get(1));

        myList.set(1, "D");
        System.out.println("Updated element at index 1: " + myList.get(1));
    }
}
```

---

ArrayList 클래스

**`ArrayList`**는 **`List`** 인터페이스를 구현하면서 필요한 메서드들의 기본 구현을 **`AbstractList`**로부터 상속받고 있음.

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {

...

public E get(int index) {
    Objects.checkIndex(index, size);
    return elementData(index);
}

...

}
```

**`AbstractList` 내부 : List인터페이스를 implments 하고 있다.**

```java
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {

...

// 유일하게 AbstractList 내에서 추상메서드로 제공함.
/**
     * {@inheritDoc}
     *
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public abstract E get(int index);

}
```

`AbstractCollection` 내

```java
public abstract class AbstractCollection<E> implements Collection<E> {

...
public abstract Iterator<E> iterator();

public abstract int size();

...
```

+) 추가

LinkedList는 `AbstractSequentialList` 를 한번 상속받고, `AbstractSequentialList` 가 `AbstractList` 을 상속받는 구조구나?