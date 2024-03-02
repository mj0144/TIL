# HTML FORM 사용

HTML FORM은 GET과 POST밖에 제공하지 않음.

(물론 AJAX같은 건 제공하나 여기서는 순수 HTML만 생각)

회원 목록 : `GET` `/members`

회원 등록 폼 : `GET` `/members/new`

회원 등록 : `POST` `/members` 또는 `/members/new`

회원 조회 : `GET` `/members/{id}`

회원 수정 폼 : `GET` `/members/{id}/edit` 

회원 수정 : `POST` `/members/{id}/edit` 또는 `/members/{id}`

회원삭제 : `POST` `/members/{id}/delete`

### 컨트롤 URI

GET, POST지원하므로 제약이 있음.

이런 제약을 해결하기 위해 동사로 된 리소스 경로 사용.

POST의 `/new`, `/edit`, `/delete` 가 컨트롤 URI

HTTP 메서드로 해결하기 애매한 경우 사용 (HTTP API 포함)

**( 단, 최대한 리소스 URI로 해결하도록! )**

→ 그치만 막상하면… 쓰는 일이 많음…