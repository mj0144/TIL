## == 연산자

비교하는 두 객체가 메모리 상에서 동일한 객체인지 확인.

기본 데이터 타입 : 값 비교

참조 타입 객체 : 참조 주소값 비교.

문자열인 경우 리터럴이 같다면(리터럴풀에서 같은 문자열을 참조하고 있다면) 

```java
String str1 = "hello";
STring str2 = "hello";

System.out.println(str1 == str2); // true
```

## equals()

Object 클래스에서 상속받은 메서드.

( 대개 클래스에서 이 메서드를 오버라이딩 해서 객체 내용을 비교하도록 구현 )

(단, **`equals()`**를 오버라이딩한 클래스에서는 반드시 **`hashCode()`**도 오버라이딩해야함..!!!)

만약 **`equals()`** 메소드를 재정의하여 두 객체가 논리적으로 동등하다고 판단한다면, 이 두 객체는 같은 해시 코드를 반환해야함.

- **`hashCode()`**를 재정의하지 않으면 기본적으로 **`Object`** 클래스의 **`hashCode()`** 메소드가 객체의 메모리 주소를 기반으로 해시 코드를 생성함.
- 해시 기반 컬렉션에서 객체를 검색할 때, **`hashCode()`**를 사용하여 먼저 해당 객체가 저장된 위치를 찾기 떄문.

→ 해시기반컬렉션( HashMap, HashSet, HashTable)를 사용하지 않는다면 굳이 재정의 할 필요는 없음.

( 하지만 유지보수성을 생각하면 하는 게 좋지.. 언제 해시기반컬렉션을 쓸지 모르잖아,… )

객체의 내용이 동일한지 확인 가능.

```java
-- 문자열 리터럴
String str1 = "hello";
String str2 = "hello";
System.out.println(str1.equals(str2)); // true

-- new String()
String str3 = new String();
String str4 = new String();

System.out.println(str3.equals(str4)); // false

```

## hashCode()


해시 기반 컬렉션은 객체를 저장할 때, hashCode()를 사용해 해시 테이블의 버킷에 저장함.

객체가 동일한 해시코드를 반환하지 않으면, 동일한 내용을 가진 객체라도 서로 다른 버킷에 저장될 수 있음.

→ 동일한 내용인데 다른 버킷에 저장된 경우 객체를 검색하거나 제거하는 과정에서 문제가 발생함.

예를 들어 HashMap에서 특정키로 검색했는데 정상적으로 검색되지 않을 수 있음.

### 예시1) HashMap에서

```java
@Override
public boolean equals(Object obj) {
    HashTest test = (HashTest) obj;
    return id == test.id && name.equals(test.name);
}

----------------------------------------------------------------------------
HashTest test1 = new HashTest(1, "testing");
HashTest test2 = new HashTest(1, "testing");

HashMap<HashTest, String> map = new HashMap<>();
map.put(test1, "hash!!!!!!!!!!");

String s = map.get(test2);
System.out.println(s); //null
```

```java
@Override
public boolean equals(Object obj) {
    HashTest test = (HashTest) obj;
    return id == test.id && name.equals(test.name);
}

@Override
public boolean equals(Object obj) {
    HashTest test = (HashTest) obj;
    return id == test.id && name.equals(test.name);
}

----------------------------------------------------------------------------
HashTest test1 = new HashTest(1, "testing");
HashTest test2 = new HashTest(1, "testing");

HashMap<HashTest, String> map = new HashMap<>();
map.put(test1, "hash!!!!!!!!!!");

String s = map.get(test2);
System.out.println(s); //hash!!!!!!!!!!
```

### 예시2) 클래스에서

```java
@Override
public boolean equals(Object obj) {
    HashTest test = (HashTest) obj;
    return id == test.id && name.equals(test.name);
}

----------------------------------------------------------------------------
HashTest test1 = new HashTest(1, "testing");
HashTest test2 = new HashTest(1, "testing");

// equals는 true를 뱉어내지만 hashCode()에서는 서로 다른 해시 코드를 뱉어낼 수 있다.
System.out.println(test1.equals(test2)); // true
System.out.println(test1.hashCode()); // 1982791261
System.out.println(test2.hashCode()); // 156255736
```

```java
@Override
public boolean equals(Object obj) {
    HashTest test = (HashTest) obj;
    return id == test.id && name.equals(test.name);
}

@Override
public int hashCode() {
    return Objects.hash(id, name);
}
-------------------------------------------------------------------------------

HashTest test1 = new HashTest(1, "testing");
HashTest test2 = new HashTest(1, "testing");

System.out.println(test1.equals(test2)); // true
System.out.println(test1.hashCode()); // -1422445072
System.out.println(test2.hashCode()); // -1422445072
```

→ 이를 방지하기 위해 **`equals()`**가 **`true`**를 반환하면 **`hashCode()`**도 같은 값을 반환하도록 재정의 필요.

```java
@Override
public int hashCode() {
    return Objects.hash(id, name);
}
```