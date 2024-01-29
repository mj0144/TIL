### 감사 트레일 : DBA_AUDIT_TRAIL, SYS.AUD$
---

DBA_AUDIT_TRAIL 와 SYS.AUD$ 테이블은 감사(Audit) 이벤트를 저장하는 데 사용되는 테이블.



## DBA_AUDIT_TRAIL 와  SYS.AUD$ 차이

###  DBA_AUDIT_TRAIL
사용자 권한 : 데이터베이스 관리자(DBA) 권한을 가진 사용자에게 대개 SELECT 권한부여 되어 접근가능.

형태 : 뷰

### SYS.AUD$
사용자 권한 : 데이터베이스 관리자(DBA) 권한이 없는 사용자도 접근 가능.
본인의 감사 이벤트 확인 가능. 단, 조작은 허용 X

형태 : 테이블

