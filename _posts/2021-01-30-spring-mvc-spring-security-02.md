---
title: Spring Security - 02
category:
- Java
tag:
- Spring Security
---

<hr/>

>>개발 환경
>>1. jdk 1.8
>>2. tomcat 8.5
>>3. eclipse 4.16
>>4. Dynamic Web Project
>>5. Spring 3.2.9
>>6. Mybatis 3.2.4
>>7. Mybatis-spring 1.2.2
>>8. Spring Security-core 3.2.6
>>9. Spring Security-config 3.2.6
>>10. Spring security-web 3.2.6

<hr/>


&nbsp;&nbsp;&nbsp;&nbsp;W : Spring Security 를 활용하여 보안을 향상시키는 기술 습득

## 학습목표
- Spring Security 설정 파일의 구성을 알 수 있다.

&nbsp;&nbsp;&nbsp;&nbsp;이번에는 지난번 게시글에서 spring-security.xml에 사용된 기본적인 태그에 대해서 정리하도록 하겠다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/security"
             xmlns:beans="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
                                 http://www.springframework.org/schema/beans/spring-beans.xsd
                                 http://www.springframework.org/schema/security
                                 http://www.springframework.org/schema/security/spring-security.xsd">
        
  <http pattern="/**/*.js" security="none"/> 
  <http pattern="/**/*.css" security="none"/> 
  <http pattern="/images/*" security="none"/> 
  <http auto-config="true">
 	<intercept-url pattern="/admin/**" access="ROLE_ADMIN"/> 
 	<intercept-url pattern="/**" access="ROLE_USER"/> 
  </http>
  <authentication-manager>
 	<authentication-provider>
 		<user-service>
 			<user name="user1" password="user1password" authorities="ROLE_USER"/>	
 			<user name="admin1" password="admin1password" authorities="ROLE_ADMIN"/>	
 		</user-service>	
 	</authentication-provider> 
  </authentication-manager>
</beans:beans>
```

&nbsp;&nbsp;&nbsp;&nbsp;먼저 짚고 넘어가야 할 태그가 있다. `<beans:beans>`태그이다. Sprin에 관한 설정 파일을 만들면서 항상 설정 파일의 상단부에는 `<beans>`로 시작해서 스키마 파일을 불러왔다. 하지만 Spring Security의 설정 파일은 `<beans>`가 아니라 `<beans:beans>`이다. 처음엔 뭐지, 하고 이상하게 생각했는데, 조금 생각해보니 `xmlns`가 `beans`가 아닌 `security`로 되어 있다. `beans`에 정의된 태그를 사용하기 위해서 접두어가 하나 더 붙은 것 뿐이었다.

#### `<http pattern="/**/*.js" security="none"/>`
&nbsp;&nbsp;&nbsp;&nbsp;security에 사용하는 `<http>`태그이다. URL 패턴을 등록하는 것으로 해당 URL로 접속이 들어오는 자바스크립트 파일은 security를 적용하지 말라는 의미이다. 이전시간 `web.xml`에 `<filter>`태그에 URL 패턴을 `/*`로 표기하여 tomcat8로 들어오는 모든 요청에 대해서 security를 적용했는데, 현재 설정 파일에서 security의 적용 범위를 한정하는 것이라고 생각된다. 차례대로 자바스크립트, CSS, 이미지 폴더 하위 경로에 해당하는 요청까지 `security` 속성을 `none`으로 처리하여 spring security를 적용하지 않도록 한다.  
&nbsp;&nbsp;&nbsp;&nbsp;`auto-config` 속성은 자동 설정에 해당한다. form 인증과 클라이언트 basic 인증, 그리고 logout에 대한 기본적인 세팅이 자동 설정된다. 실제로는 사용하지 않는 설정으로 이후 커스텀을 통해서 사용하기 때문에 해당 인증에 대해서는 커스텀을 다룰 때에 자세히 다루면 될 것 같다.  
&nbsp;&nbsp;&nbsp;&nbsp;`<intercept-url>`는 요청하는 URL 패턴에 따라 접근 권한을 설정하는 것이다. `/admin/**`은 `/admin/`하위의 경로로 접근하려는 모든 요청은 `ROLE_ADMIN` 권한을 가지고 있어야만 접근할 수 있는 것이다. 대문자로 표기했으니 어림짐작으로 변수가 아닌 상수임을 알 수 있다. 그 외의 경로는 `ROLE_USER` 권한을 가지고 있어야 접근이 가능하다. 패턴의 범위에 따라서 오동작을 하는 경우도 있으니, 구체적인 URL 패턴의 접근 권한을 먼저 설정해야 한다.

#### `<user-service>`
&nbsp;&nbsp;&nbsp;&nbsp;`<authentication-manager>`태그는 인증과 관련된 정보를 설정한다. 인증을 하기 위해서는 인증에 대한 정보를 제공하는 제공자를 지정해야 하는데, 그 기능을 하는 태그가 `<authentication-provider>`태그이다. 이 태그의 하위 태그인 `<user-service>`태그 하위에 `<user>`태그에 속성값을 정의한다. `name`은 유저 아이디, `password`는 유저 비밀번호이며, `authorities`는 해당 유저의 권한을 의미한다. Spring Security 4버전 이후부터는 모든 비밀번호를 암호화해야 사용이 가능하다고 하나, 지금 버전은 3.2.6이기 때문에 특별히 암호화를 하지 않아도 된다. 암호화를 위한 클래스도 Spring Security에 존재하는 것 같으니, 이후 Spring Security를 커스텀하면서 게시글에 게시하게 될 것 같다.

&nbsp;&nbsp;&nbsp;&nbsp;굉장히 기본적인 설정이라 여기서 설정 파일의 내용이 끝났다. 다음 게시글에는 maven이나 gradle을 이용해서 Spring Security를 설정하는 방법에 대해서 게시하고 커스텀으로 넘어가는게 좋을 것 같다. 

L : Spring Security의 기본 설정 파일에 대해서 알게 되었다.

<hr>

_참고문헌_  

제타건남님의 블로그 : <https://zgundam.tistory.com/44?category=430446>  
미니멀리스트 개발자님의 블로그 : <https://admm.tistory.com/106>
