---
title: Spring Security - 04
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
- Spring Security의 로그인 화면을 커스터마이징하기

&nbsp;&nbsp;&nbsp;&nbsp;지난시간 Spring Security의 기본 로그인 화면을 살펴보고 어떻게 이루어져 있는지를 간단하게 살펴보았다. 로그인에 대한 기능은 있지만, 이 상태로는 어느 프로젝트에도 적용을 할 수가 없기 때문에 이 로그인 화면을 커스터마이징을 해서 쓸 수 있을 정도로 만들어 놓아야 한다.

#### `<form-login>` 설정하기

&nbsp;&nbsp;&nbsp;&nbsp;먼저 로그인 페이지를 만들어야 한다. 기본적으로 Spring Security에서 준 로그인 페이지는 수정할 수 없는 걸로 알고 있다. 따라서 개발자가 원하는 형태의 로그인 페이지를 구현해놓고, Spring Security 설정 파일에서 매핑을 시켜주어야 한다. 매핑을 시켜줄 때 로그인 기능에서 기본적으로 있어야만 하는 것이 바로  ID와 PW이기 때문에 설정 파일에는 ID와 PW를 어떤 파라미터로 보내야 하는지까지 설정해줘야 한다. 즉, 로그인 페이지 URL, ID 파라미터와 PW 파라미터를 설정 초기에는 세팅해주지 않아서 Spring Security의 기본 설정 값으로 보내주던 것을 개발자가 직접 값을 넣어서 경로나 값을 변경시켜줘야 한다. 이를 설정하기 위해서는 `<http>` 하위계층 태그에 `<form-login>` 태그를 정의하고 속성값을 주어야 한다.

```xml
<http auto-config="true">
    <form-login
 		username-parameter="loginid"	
 		password-parameter="loginpw"
 		login-page="/login.do"
 		default-target-url="/main.do"
 		authentication-failure-url="/login.do?fail=true" />
</http>
```

&nbsp;&nbsp;&nbsp;&nbsp;`<http>`에서 auto-config 속성은 여러 페이지의 값을 기본적으로 설정해주는 속성이며, 그 페이지에는 로그인 페이지도 포함되어 있다. 하위 태그에 `<form-login>`을 정의해서 로그인 페이지를 개발자가 직접 설정해주는 것이다.

- `username-parameter="loginid"` : 로그인 페이지의 ID 파라미터를 loginid로 정의한다.
- `password-parameter="loginpw"` : 로그인 페이지의 PW 파라미터를 loginpw로 정의한다.

&nbsp;&nbsp;&nbsp;&nbsp;위와 같이 설정하면, 개발자가 매핑한 로그인 페이지에서 ID와 PW를 사용한 input 태그를 작성할 때, name을 loginid, loginpw로 설정해서 사용할 수 있다. 정상적으로 Spring Security가 동작한다.

- `login-page="/login.do"` : 기본 로그인 페이지가 아니라 개발자가 작성한 로그인 페이지로 요청한다.
- `default-target-url-"/main.do"` : 로그인에 성공하면 해당 "main.do"를 요청한다.
- `authentication-failure-url="/login.do?fail=true"` : 로그인에 실패하면 보여줄 페이지를 요청한다. 파라미터에 값을 주어 해당 페이지에 특정 문구를 보여주게 할 수 있다.

#### `<intercept-url>` 설정하기 - Spring EL 태그

&nbsp;&nbsp;&nbsp;&nbsp;다음으로 `<http>`의 `<intercept-url>`의 태그를 수정할 필요가 있다. 권한 그룹을 access에 집어넣는 것도 물론 유효한 방법이지만, 다양한 권한을 관리해야 하는 페이지라면 해당 권한 그룹을 일일이 나열해서 집어넣는 것은 낭비이다. 이를 위해서 Spring Security에는 Spring EL 태그를 사용해서 권한을 조금 더 쉽게 설정할 수 있다.

```xml
<!-- use-expressions="true" 로 설정해서 Spring EL 태그를 활성화한다. -->
<http auto-config="true" use-expressions="true">
 	<intercept-url pattern="/admin/**" access="hasRole('ROLE_ADMIN')"/> 
 	<intercept-url pattern="/login.do" access="isAnonymous()"/> 
 	<intercept-url pattern="/main.do" access="permitAll" />
 	<intercept-url pattern="/**" access="permitAll" />
    <form-login
 		username-parameter="loginid"	
 		password-parameter="loginpw"
 		login-page="/login.do"
 		default-target-url="/main.do"
 		authentication-failure-url="/login.do?fail=true" />
</http>
```

 - hasRole(role1) : 인자로 들어간 권한을 가지고 있는 경우 access 허용한다.
 - isAnonymous() : Anonymous 사용자일 경우 access 허용한다 >> 로그인 X
 - permitAll : 권한 여부 상관 없이 access 허용한다.

&nbsp;&nbsp;&nbsp;&nbsp;이러면 spring-security.xml 설정은 끝난다. 다음은 이를 적용해서 만든 간단한 로그인 페이지이다.

```jsp
<body>
	<div style="display:inline-block;">
		로그인 화면
		<form id="loginfrm" name="loginfrm" action="<c:url value='${ctx}/j_spring_security_check'/>" method="POST">
			<table>
				<tr>
					<td>아이디</td>
					<td>
						<input type="text" id="loginid" name="loginid" value="" />	
					</td>
					<td rowspan="2">
						<input type="button" id="loginbtn" value="확인" />	
					</td>
				</tr>	
				<tr>
					<td>비밀번호</td>
					<td>
						<input type="text" id="loginpw" name="loginpw" value="" />	
					</td>
				</tr>	
				<c:if test="${not empty param.fail}"> 
				<tr>
					<td colspan="2">
						<font color="red">
							<p>Your login attempt was not successful, try again</p>	
							<p>Reason : ${sessionScope["SPRING_SECURITY_LAST_EXCEPTION"].message}</p>	
						</font>	
						<c:remove scope="session" var="SPRING_SECURITY_LAST_EXCEPTION"/>
					</td>	
				</tr>
				</c:if>
			</table>	
		</form>
	</div>
</body>
```

&nbsp;&nbsp;&nbsp;&nbsp;위 코드를 보면 아이디, 비밀번호의 `<input>`태그의 name 속성이 spring-security.xml의 username-parameter, password-parameter와 일치하는 것을 알 수 있다. `<form>`태그의 action 속성이 jstl태그로 되어 있는데, Spring Security를 적용하기 위해서 해당 경로로 요청을 보낸다. `<c:if>`로 되어 있는 jstl 태그는 해당 페이지에 fail 파라미터를 가지고 접근하면 코드블럭을 실행하라는 의미이며, authentication-failure-url로 로그인 실패시에 fail=true로 보냈기 때문에 로그인에 실패하면 아래 코드를 실행한다. 로그인 실패 이유가 조금 복잡하게 되어 있는데, 이는 Spring-Security-core의 라이브러리에 들어 있는 message property 파일의 값을 가져온 것이다. 이 파일은 각종 메시지 key=value로 들어있는데, 언어별로 메시지가 들어있기 때문에 한국어도 포함되어 있다. `<p>`태그에 있는 EL 태그가 SPRING_SECURITY_LAST_EXCEPTION의 결과로 Bad credentials가 출력되며, 이를 한국어 메시지 파일에서 찾아내서 결과값을 보여준다. 현재 커스터마이징 된 내 페이지에서는 `Reason : 비밀번호(credential)가 맞지 않습니다.` 라고 출력된다.  
&nbsp;&nbsp;&nbsp;&nbsp;물론 한국어 메시지를 보기 위해서는 해당 라이브러리의 압축을 해제해서 한국어로 된 메시지 파일을 찾아야 하고, 이를 읽어주도록 설정 파일을 변경해야 한다. 나는 한번 `root-context.xml`에 넣어봤는데 제대로 동작했다. 아래 코드는 그에 대한 코드이다.

```xml
 <bean id="messageSource"
 	class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
 		<property name="basenames">
 			<list>
 				<value>/WEB-INF/messages/spring_security_messages</value>	
 			</list>	
 		</property>
 		<property name="defaultEncoding" value="UTF-8" />
 		<property name="cacheSeconds" value="5" />
```

L : Spring Security의 로그인 페이지를 커스터마이징 할 수 있게 되었다.

<hr>

_참고문헌_

제타건남님의 블로그 : <https://zgundam.tistory.com/46?category=430446>, <https://zgundam.tistory.com/47?category=430446>