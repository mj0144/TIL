두 클래스 모두 `가변성(mutability)`를 가진다.

## String

`불변(immutable)`클래스이며, 문자열을 변경하는 경우 새로운 문자열이 생성됨. 

이전 문자열은 가비지 컬렉션의 대상이 됨.

(단, “”(문자열 리터럴)은 리터럴 풀(상수풀, class영역)에 존재( 상수로 취급되어 클래스 파일에 저장)

.같은 문자열인 경우 동일한 객체 참조.)

[String](https://www.notion.so/String-fd01ab3ecea04f3e8eb9eff644365b16?pvs=21) 

## StringBuffer

스레드에 안전(thread-safe)하게 동기화 처리가 되어 여러 쓰레드가 접근해도 안전 

→ 멀티스레드 환경에서 안전하게 사용가능하다.

## StringBuilder

스레드에 대한 동기화를 지원하지 않음.

→ 따라서 단일스레드 환경에서 빠르다.