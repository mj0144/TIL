### 감사(Audit)

---

- 사용자가 데이터를 조회, 조작할 때마다 에이 대한 정보를 기록하여 누가/언제/무엇을 했는지 확인 하는 방법.
- 감사로 인해 생성된 이벤트는 감사 트레인(Audit Trail)에 기록.
  - 감사 트레일은 감사 로그 파일에 정보를 저장 및 설정에 따라 파일이나 DB 테이블에 저장가능.
  - 대개 DBA_AUDIT_TRAIL 또는 sys.aud$에 저장.
  - DBA_AUDIT_TRAIL VS SYS.AUD$

### Auditing

---

- 사용자의 행동을 감시하거나 DB에 관한 통계자료를 얻는 목적으로 사용됨.

### 감사 사용 이유

---

- DB 권한을 받은 사용자가 주어진 권한을 이용하여 원래의 목적에 맞지 않거나 접근해서는 안되는 중요한 데이터를 조회하거나 변경할 수 있으므로, 이를 막기 위해 ‘감사(Audit)’를 사용함.


### sys.aud$ 테이블

---

- 감사(Audit) 에 대한 결과를 저장해놓은 테이블
```
-- 감사 이벤트 테이블
SELECT * FROM SYS.AUD$;
```

```
-- 
SELECT USERNAME, TIMESTAMP, ACTION_NAME, OBJECT_NAME
FROM DBA_AUDIT_TRAIL
WHERE AUDIT_TYPE = 'SELECT';
```



### 감사 활성화 확인 및 설정
---

```
-- 감사 활성화 여부 확인
SELECT * FROM DBA_PRIV_AUDIT_OPTS;
```


```
-- 모든 사용자의 로그인을 감사.
AUDIT CREATE SESSION;

-- 모든 테이블에 대한 select 문 감사
AUDIT SELECT TABLE;
```

```
-- 감사 활성화
ALTER SYSTEM SET AUDIT_TRAIL=DB, EXTENDED SCOPE=SPFILE;
```

```
-- 위 내용 적용하기 위해서는 DB 재시작 또는 ALTER SYSTEM 명령어 사용.
SHUTDOWN IMMEDIATE;
STARTUP;
```





