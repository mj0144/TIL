@Transactional은 AOP(Aspect-Oriented Programming) 기반으로 동작
이는 메소드 실행을 가로채어 트랜잭션을 시작, 커밋, 롤백하는 로직을 적용하는 방식.

Spring은 이를 위해 프록시 패턴을 사용하여 실제 객체 대신 프록시 객체를 생성하고, 해당 프록시 객체를 통해 메소드 호출을 관리함.

1. 프록시 생성
    
    Spring은 **`@Transactional`** 어노테이션이 붙은 클래스나 메소드를 찾아 해당 객체의 프록시를 생성함.(  JDK 동적 프록시(인터페이스 기반) 또는 CGLIB 프록시(클래스 기반)를 사용하여 생성 )
    
    ApplicationContext가 구성될 때 BeanPostProcessor를 통해 처리됨.
    
    - **JDK 동적 프록시**: 클래스가 인터페이스를 구현하고 있는 경우
        
        → Spring은 JDK의 **`Proxy`** 클래스를 사용해 인터페이스 기반의 프록시 객체를 생성.
        
    - **CGLIB 프록시**: 타깃 객체가 인터페이스를 구현하고 있지 않은 경우
        
        → Spring은 `CGLIB 라이브러리`를 사용하여 클래스를 상속받는 방식으로 프록시 객체를 생성.
        
2. 메서드 인터셉트
    
    프록시 객체는 실제 객체 대신 호출을 받아, **`@Transactional`** 어노테이션이 붙은 메소드의 호출을 가로챔.
    
    이때, AOP의 어드바이스(Advice)가 실행되어 트랜잭션 관련 로직이 처리하고,  Spring은 **`TransactionInterceptor`**를 사용해 트랜잭션을 관리함.
    
3. 트랜잭션 관리
    
    **`@Transactional`** 어노테이션이 붙은 메소드 호출을 인터셉트하면, **`TransactionInterceptor`**는 다음과 같은 과정을 수행함.
    
    1. **`트랜잭션 시작`**: 현재의 트랜잭션 상태 및 트랜잭션 속성(전파 방식, 격리 수준, 타임아웃 등)을 확인하여 이에 따라 트랜잭션을 어떻게 처리할지 결정.
    2. **`메소드 실행`**: 실제 대상 메소드를 실행하며, try-catch 블록 내에서 실행됨.
    3. **`트랜잭션 커밋 또는 롤백`**: 메소드 실행이 성공여부에 따라 커밋 또는 롤백을 진행.
    

@Transactional 의 단점

1. 모든 설정은 컴파일 타임에 결정되므로 런타임에 트랜잭션 동작을 동적으로 변경할 수 없음.
2. AOP 프록시 기반 한계.
    
    @Transactional은 어노테이션이 붙은 클래스나 메소드에 대해 프록시 객체를 생성하여, 해당 객체의 메소드 호출을 가로채고 트랜잭션 관리 로직을 적용함. 
    
    실제 객체(타깃 객체)에 대한 참조를 가지고 있으며, 외부로부터의 메소드 호출을 받으면 이를 타깃 객체로 전달하기 전에 트랜잭션 처리를 수행하는 식임.
    
    `프록시 기반의 한계`는 주로 자기 호출, 즉 같은 객체 내부의 메소드가 다른 **`@Transactional`** 메소드를 직접 호출할 때 발생함.
    
    이런 경우, **외부에서 프록시를 통해 메소드를 호출할 때만 트랜잭션 관리가 적용되고, 객체 내부의 메소드 호출은 프록시를 거치지 않기 때문에 트랜잭션 어드바이스가 적용되지 않음.**
    
    예를 들어, **`@Transactional`** 어노테이션이 붙은 **`methodA`**에서 같은 클래스 내의 **`@Transactional`** 어노테이션이 붙지 않은 **`methodB`**를 호출하거나,  **`@Transactional`**이 붙은 **`methodB`**를 호출한다 하더라도, 이런 내부 호출은 프록시를 통하지 않기 때문에 **`methodB`**에 대한 트랜잭션 처리는 이루어지지 않습니다.
    
    ```java
    @Service
    public class UserService {
    
        @Transactional
        public void updateUser(Long userId) {
            etcMethod(); // etcMethod메서드는 프록시를 통하지 못하고 실제메서드가 호출됨. -> 따라서 트랜잭션 관리는 받지 못함.
        }
    
        public void etcMethod() {
            // 추가로직
        }
    }
    
    ```
    

해결방안

위와 같은 아래와 같은 해결.

1. 한계 때문에 내부 호출을 하지 않도록 구조를 구성하는 것이 좋음.
    
    이러한 구조는 자기 호출을 필요로 하는 로직을 외부 클래스로 분리하고, 해당 클래스를 Spring Bean으로 등록하여 주입받아 사용하는 것
    
    ```java
    // 기존 매서드를 외부 클래스로 분리.
    @Service
    public class EtcService {
    
        @Transactional
        public void etcMethod() {
            // 추가로직
        }
    }
    
    @Service
    public class UserService {
    
        private final EtcService etcService ;
    
        public UserService(EtcService etcService) {
            this.etcService = etcService;
        }
    
        @Transactional
        public void updateUser(Long userId) {
            etcService .helperMethod(); // 이렇게 하면 프록시가 호출됨.
        }
    }
    
    ```
    

1. Spring AOP Context 사용.
    
    Spring AOP의 현재 프록시를 사용.
    
    →  이 방법은 같은 클래스 내에서 자기 자신의 프록시에 접근하여 메소드를 호출할 수 있게 함.
    
    **`AopContext.currentProxy()`**를 사용하며,  Spring 설정에서 **`exposeProxy = true`**를 활성화해야 함. ( **`@EnableAspectJAutoProxy(exposeProxy = true)` )**
    
    ```java
    @Service
    **@EnableAspectJAutoProxy(exposeProxy = true)**
    public class UserService {
    
        @Transactional
        public void updateUser(Long userId) {
        // AopContext.currentProxy()를 통해 현재 객체의 프록시를 얻고, 이를 통해 etcMethod를 호출.
            ((UserService) **AopContext.currentProxy()**).etcMethod();
        }
    
        @Transactional
        public void etcMethod() {
            // 추가로직
        }
    }
    
    ```
    

→ 유지보수성과 재사용성을 생각하면 외부 클래스로 빼는 것이 좋겠지.