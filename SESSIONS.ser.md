### 브라우저 세션

- 세션은 브라우저 단위로 생성되고 종료되는 객체임. 아래 조건이 하나라도 만족되었을 때 세션이 종료됨.
    1. 사용자가 브라우저를 종료
    2. 소스상 HttpSession#invalidate() 메서드가 호출되었을 때
    3. 마지막으로 설정된 timeouy에 의해 세션이 만료되었을 떄. 

### 톰캣 세션

- 톰캣의 경우 브라우저와는 다르게  org.apache.catalina.session.StandardManager 라는 세션매니저를 통해 세션관리됨.
- 이 세션매니저는 톰캣이 종료될 때 살아있는 세션을 임시저장소에 SESSIONS.ser라는 파일로 저장해두고, 재시작될 때 이 파일을 읽어서 세션을 다시 되살림.
    - 만일 톰캣이 종료될때 가지고 있떤 세션을 모두 제거하기 위해서는 설정이 필요함.
    

### SESSIONS.ser 파일이 저장되는 경로

- <Host> 또는 <Context>의 workDir 속성으로 지정한 디렉토리에 저장됨.
- 기본적으로는 $CATALINA_HOME/work 하위의 웹 어플리케이션 경로에 있음.
    - ($CATALINA_HOME : 환경변수)
    
- 크리젠전산의 SESSIONS.ser 파일경로
    - : /usr/local/java/apache-tomcat-7.0.105-ncf-nx/work/Catalina/localhost/_/SESSIONS.ser
    

### 세션 저장 설정 비활성화

- 톰캣이 설치된 디렉토리인 $CATALINA_HOME/conf/context.xml 파일에서 수정.
- 파일 내에서 아래부분을 주석을 해제하면 세션 저장기능이 비활성화 됨.

```jsx
<!-- Uncomment this to disable session persistence across Tomcat restarts -->
<!--<Manager pathname="" />-->
```

출처: https://dololak.tistory.com/738