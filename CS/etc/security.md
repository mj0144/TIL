## `DelegatingFilterProxy`

security는 기본적으로 필터로 동작.

하지만 필터는 spring container 밖에 있으므로 Bean을 인식할 수 없음.

이것을 해결하기 위해 `DelegatingFilterProxy`를 두어 요청을 위임함.

<img src="image-1.png" width="250" height="300" />

<br>

## **FilterChainProxy**

<img src="image-2.png" width="350" height="300" />

DelegatingFilterProxy는 자체에 보안로직이 구현되어 있지 않으며, `FilterChainProxy` 와 같은 Spring bean 의 메서드를 호출하여 처리를 위임(Delegate)함.

`FilterChainProxy` 는 `SecurityFilterChain` 의 많은 필터 리스트를 가지고 있으며, 이는 각 HTTP 요청에 대해 적용되어야 하는 필터를 가지고 있음.

→ HTTP 요청이 들어오면 `FilterChainProxy` 은 요청에 맞는 `SecurityFilterChain` 을 찾아 실행.

## **SecurityFilterChain**

`SecurityFilterChain`은 현재 요청에 대해 호출되어야 하는 Spring Security Filter 인스턴스를 결정하기 위해 `FilterChainProxy`에서 사용됩니다.

<img src="image-3.png" width="350" height="300" />
서블릿 컨테이너에서 필터 인스턴스는 URL만을 기반으로 호출됩니다. 

그러나 `FilterChainProxy`는 RequestMatcher 인터페이스를 사용하여 HttpServletRequest의 모든 항목을 기반으로 호출을 결정할 수 있습니다.

<img src="image-4.png" width="350" height="300" />

Multiple SecurityFilterChain 그림에서 `FilterChainProxy`는 어떤 `SecurityFilterChain`을 사용해야 하는지 결정합니다. 

일치하는 첫 번째 SecurityFilterChain만 호출됩니다. 

`/api/messages/`의 URL이 요청되면 먼저 `/api/**`의 `SecurityFilterChain0` 패턴과 일치하므로 SecurityFilterChain(n)에서도 일치하더라도 SecurityFilterChain0만 호출됨.

`/messages/`의 URL이 요청되면 /api/**의 SecurityFilterChain0 패턴과 일치하지 않으므로 **FilterChainProxy는 계속해서 각 SecurityFilterChain을 시도함.**

일치하는 다른 `SecurityFilterChain`인스턴스가 없다고 가정하면 `SecurityFilterChain(n)`이 호출됩니다.

`SecurityFilterChain0` 와 같이 보안 필터 인스턴스가 3개만 구성될 수도 있고, 

`SecurityFilterChain(n)` 와 같이 4개의 보안 필터 인스턴스가 구성될 수도 있음.

 

**각 SecurityFilterChain은 고유할 수 있으며 별도로 구성될 수 있음.**

실제로, 애플리케이션이 Spring Security가 특정 요청을 무시하도록 하려는 경우 `SecurityFilterChain`에는 보안 필터 인스턴스가 없을 수 있음.

## Security Filters

보안 필터는 `SecurityFilterChain API`를 사용하여 `FilterChainProxy`에 삽입됩니다. 

이러한 필터는 인증, 권한 부여, 악용 방지 등과 같은 다양한 목적으로 사용될 수 있습니다. 

**필터는 적시에 호출되도록 특정 순서로 실행**됩니다. 

예를 들어 인증(authentication)을 수행하는 필터는 인가(authorization) 수행하는 필터보다 먼저 호출되어야 합니다. 

일반적으로 Spring Security 필터의 순서를 알 필요는 없으나, 만일 순서를 알고 싶다면 `FilterOrderRegistration` 코드를 확인하면 됨.

### 보안 구성 예시

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(Customizer.withDefaults())
            .authorizeHttpRequests(authorize -> authorize
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults())
            .formLogin(Customizer.withDefaults());
        return http.build();
    }

}
```

위 코드의 필터 순서는 아래와 같음.

1. [CsrfFilter](https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html) <br>- `HttpSecurity#csrf` : CSRF 공격 보호
2. [UsernamePasswordAuthenticationFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/form.html#servlet-authentication-form)<br> - `HttpSecurity#formLogin` : 요청인증
3. [BasicAuthenticationFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/basic.html)<br> - `HttpSecurity#httpBasic`
4. [AuthorizationFilter](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)<br> - `HttpSecurity#authorizeHttpRequests` : 요청 승인

### Printing the Security Filters

특정 요청에 대해 호출되는 보안 필터 목록을 확인할 떄 사용.

예를 들어, 추가한 필터가 보안 필터 목록에 있는지 확인할 경우
필터 목록은 애플리케이션 시작 시 INFO 수준에서 콘솔 출력에서 아래와 같이 출력.

```java
2023-06-14T08:55:22.321-03:00  INFO 76975 --- [           main] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with [
org.springframework.security.web.session.DisableEncodeUrlFilter@404db674,
org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter@50f097b5,
org.springframework.security.web.context.SecurityContextHolderFilter@6fc6deb7,
org.springframework.security.web.header.HeaderWriterFilter@6f76c2cc,
org.springframework.security.web.csrf.CsrfFilter@c29fe36,
org.springframework.security.web.authentication.logout.LogoutFilter@ef60710,
org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter@7c2dfa2,
org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter@4397a639,
org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter@7add838c,
org.springframework.security.web.authentication.www.BasicAuthenticationFilter@5cc9d3d0,
org.springframework.security.web.savedrequest.RequestCacheAwareFilter@7da39774,
org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter@32b0876c,
org.springframework.security.web.authentication.AnonymousAuthenticationFilter@3662bdff,
org.springframework.security.web.access.ExceptionTranslationFilter@77681ce4,
org.springframework.security.web.access.intercept.AuthorizationFilter@169268a7]
```

### Adding a Custom Filter to the Filter Chain

 보안 필터 체인에 사용자 지정 필터를 추가하려는 경우 사용.

예를 들어 **Tenant**  ID 헤더를 가져오는 필터를 추가하고 현재 사용자가 해당 **Tenant** 에 액세스할 수 있는지 확인한다고 가정해 보겠습니다.  현재 사용자를 알아야 하므로 인증 필터 뒤에 추가해야 합니다.

+)**Tenant(임차인)  : 자신의 자원이 아닌 서비스 제공자의 클라우드 자원을 빌려서 서비스를 이용하는 주체가 Tenant.**

```java
import java.io.IOException;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import org.springframework.security.access.AccessDeniedException;

public class TenantFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;

        String tenantId = request.getHeader("X-Tenant-Id"); (1)
        boolean hasAccess = isUserAllowed(tenantId); (2)
        if (hasAccess) {
            filterChain.doFilter(request, response); (3)
            return;
        }
        throw new AccessDeniedException("Access denied"); (4)
    }

}
```

위의 샘플 코드는 다음을 수행합니다.

1. 요청 헤더에서 **Tenant**ID를 가져옵니다.
2. 현재 사용자가 **Tenant**ID에 액세스할 수 있는지 확인하세요.
3. 사용자에게 액세스 권한이 있으면 체인의 나머지 필터를 호출합니다.
4. 사용자에게 액세스 권한이 없으면 AccessDeniedException이 발생합니다.

필터를 구현하는 대신 요청당 한 번만 호출되는 필터의 기본 클래스인 `OncePerRequestFilter`에서 확장할 수 있으며 HttpServletRequest 및 HttpServletResponse 매개변수와 함께 doFilterInternal 메소드를 제공합니다.

**이제 보안 필터 체인에 필터를 추가.**

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        // ...
        .addFilterBefore(new TenantFilter(), AuthorizationFilter.class);
    return http.build();
}
```

**`AuthorizationFilter` 앞에 TenantFilter를 추가하려면 HttpSecurity#addFilterBefore를 사용.**

`AuthorizationFilter` 앞에 필터를 추가함으로써 인증 필터 다음에 TenantFilter가 호출되도록 합니다. 

HttpSecurity#`addFilterAfter`를 사용하여 특정 필터 뒤에 필터를 추가하거나 HttpSecurity#`addFilterAt`를 사용하여 필터 체인의 특정 필터 위치에 필터를 추가할 수 있음.

 **종속성 주입을 활용하고 중복 호출을 피하기 위해 필터를 Spring 빈으로 선언하려는 경우 FilterRegistrationBean 빈을 선언하고 활성화된 속성을 다음과 같이 설정하여 Spring Boot에 컨테이너에 등록하지 않도록 지시할 수 있습니다**

```java
@Bean
public FilterRegistrationBean<TenantFilter> tenantFilterRegistration(TenantFilter filter) {
    FilterRegistrationBean<TenantFilter> registration = new FilterRegistrationBean<>(filter);
    registration.setEnabled(false);
    return registration;
}
```