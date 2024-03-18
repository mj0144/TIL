# JWT(Json Web Token)

JRFC 7519 웹 표준으로 지정이 되어있으며 Json 객체를 사용해서 토큰 자체에 정보들을 저장하고 있는 Web Token(웹 기반 인증 및 정보 교환을 위한 방식 중 하나로 사용되는 토큰).

JWT의 구조는 `header(json)` + `payload(json)` + `signature` .

`header`에는 signature를 해싱하기 위한 알고리즘 정보.

`payload` 에는 서버와 클라이언트가 주고받기로 한 값.

`signatrue`에는 토큰의 유효성을 검증하기 위한 문자열.

### 흐름.

클라이언트 로그인 시도 

→ 서버에서 payload에 주고받고싶은 데이터를 담아 서명(signature)하고 Token을 발행.

→ 클라이언트는 다음 통신부터 Token값과 함께 요청을 전달 

→ 서버에서는 Token을 검증 후 로직 수행.

# 구조

JWT는 Header, Payload, Signature로 구성된다. 각 요소는 .으로 구분됨.

```java
eyJhbGciOiJIUzI1NiJ9. -- header eyJzdWIiOiJUZXN0NCIsInJvbGVzIjpbIlJPTEVfVVNFUiJdLCJleHAiOjE3MTA3NDYxNjcsImlhdCI6MTcxMDc0MjU2N30. -- payload
fvIe76mVySiW2OdXgAX4VaPNf3RsUNXfpJnYdlf0Cbg -- signature
```

## Header

JWT에서 사용할 타입(typ)과 해시 알고리즘(alg)의 종류가 담겨있다.

```json
{
  "typ": "JWT",
  "alg": "HS512"
}
```

## Payload

서버에서 첨부한 사용자 권한 정보와 데이터인 클레임(Claim) 담겨있다. 클레임은 Key/Value 형태로 된 값을 가진다. 저장되는 정보에 따라 `Registered Claims`, `Public Claims`, `Private Cliams`로 구분된다.

### Registered Claims( 등록된 클레임 )

iss : 토큰 발급자(issuer)

sub : 토큰 제목(subject)

aud : 토큰 대상자(audience)

exp : 토큰 만료 시간(expiration)

nbf : 토큰 활성 날짜(note before) ( 해당 날짜가 지나기 전의 토큰은 활성화 안됨 )

iat : 토큰 발급 시간(issued at)

jti : JWT 토큰 식별자. 중복을 방지하기 위해 사용. 일회용 토큰(Access Token)등에 사용.

```jsx
{
  "sub": "subject",
  "iss": "abc",
}
```

### **공개 클레임(Public Claims)**

공개 클레임들은 충돌이 방지된 이름을 가지고 있어야한다. 주로 URI 형식으로 짓는다.

```json
{
  "https://velog.io/jwt/public": true
}
```

### **비공개 클레임(Private Cliams)**

클라이언트와 서버 협의하에 사용되는 클레임이다. 공개 클레임과는 달리 이름이 중복되어 충돌될 수 있기 때문에 유의해야 한다.

```json
{
  "username": "kim"
}
```

→ 위와 같이 3가지 종류로 나눠져 있기는 하나, 공개 클레임인지 비공개 클레임인지를 직접적으로 구분해주는 명시적인 표시나 플래그는 없음.

클레임의 종류는 주로 그것들이 어떻게 사용되고, 어떤 약속 또는 규약 정도의 개념으로 생각.

## Signature

Signature은 **헤더(Header)와 페이로드(Payload)**의 값을 각각 BASE64로 인코딩.

→ 인코딩한 값을 비밀키로 해싱(이때 비밀키로 해싱하는 알고리즘은 (Header)에서 정의한 알고리즘 사용)

→ 이 값을 다시 BASE64로 인코딩하여 생성

## JWT Singature 알고리즘

### 대칭 키 알고리즘(**Symmetric Key Algorithms)**

- **`HS256` (HMAC with SHA-256)**: 가장 널리 사용되는 대칭 키 알고리즘 중 하나입니다.
    - 서명을 생성하고 검증하는 데 동일한 비밀 키를 사용합니다. 간단하고 빠르지만, 키 관리가 중요한 도전 과제가 될  수 있음.
    - 토큰을 만들때 사용한 secret_key와 토큰을 검증할때 사용하는 secret_key가 같아야 함.



### **비대칭 키 알고리즘 (Asymmetric Key Algorithms)**

- **`RS256` (RSA Signature with SHA-256)** : 공개 키/개인 키 쌍을 사용하여 서명을 생성하고 검증합니다. **서명 생성에는 개인 키(private key)**를 사용하고, **검증에는 공개 키(public key)**를 사용합니다. 키 관리가 대칭 키 알고리즘보다 더 복잡할 수 있지만, 보안성이 높고 서버와 클라이언트 간에 키를 안전하게 교환할 필요가 없습니다.
    
    public key는 jwk를 통해 공개적으로 제공
    
- **`ES256` (ECDSA using P-256 and SHA-256)**: 타원 곡선 디지털 서명 알고리즘(ECDSA)을 사용합니다. RS256에 비해 키 크기가 작고, 같은 수준의 보안을 제공하면서 더 빠른 처리 속도를 보여줍니다.

# JWS(Json Web Signature)

서버에서 인증을 근거로 인증정보를 서버의 private key로 서명(Signature) 한것을 토큰화 한 것.

JWS는 단독으로 사용될 수 있으며, JWT의 서명 부분을 구현하는 데 사용되기도 합니다. 

즉, **모든 JWT는 JWS의 형식을 따르지만, JWS가 항상 JWT를 의미하는 것은 아님.**

`JWS`는 정보의 무결성과 인증을 위한 메커니즘이며, 

`JWT`는 그러한 메커니즘을 사용하여 사용자 인증 정보나 기타 데이터를 안전하게 전송하는 데 사용되는 토큰 형식

### JWT 검증 예외

1. `ExpiredJwtException` : JWT의 유효시간 초과했을 경우
2. `UnsupportedJwtException` : JWT의 형식이 일치 하지 않을 경우
3. `MalformedJwtException` : JWT가 올바르게 구성되지 않을 경우
4. `SignatureException` : JWT의 기존 signature 검증이 실패 했을 경우
5. `PrematureJwtException` : nbf를 선언했을 경우 토큰 유효 시간전에 사용했을 경우
6. `ClaimJwtException` : JWT에서 권한 Claim 검사를 실패했을 경우