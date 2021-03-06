---
title: Spring Security - 01
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
- Spring Security로 로그인 페이지 보기

&nbsp;&nbsp;&nbsp;&nbsp;나는 Spring을 배우기 이전에 Python Django로 간단한 게시판을 만든적이 있다. UI나 구성을 전혀 고려하지 않고 기능에만 초점을 두었기 때문에 굉장히 조악하지만 근무하고 있던 공장에서 직원들간의 이슈를 교류하기에는 충분했다고 생각한다. 물론 내가 퇴사한 이후에 다시 다른 서비스(아마 기존에 사용하던 네이트온)을 선택해서 활용하고 있는 것 같지만, 적어도 실제 사업장에서 내 게시판을 활용해서 생산적인 일을 진행하는 모습을 본 경험은 의미가 있다고 생각한다.  
&nbsp;&nbsp;&nbsp;&nbsp;Django 프레임워크에서 초심자인 내가 가장 기억에 남는 것은 웹을 만들면 자동으로 매칭되는 관리자 페이지까지 생성되는 것이다. 관리자페이지는 관리자만 확인하면 되기 때문에 굳이 정성들여 페이지를 만들 필요가 없다. 관리자 페이지는 그냥 알아볼 수만 있으면 넘어가자는게 개발자들의 생각인 것 같다.  
&nbsp;&nbsp;&nbsp;&nbsp;그런데 Spring에서 처음 웹을 만들었을때, 관리자 페이지는 보이지 않았다. 지금까지 진행한 프로젝트에서 게시판을 적어도 3번정도 만들었는데, 전부 다 관리를 할 수 없는 페이지만 만들고 있었던 것이다. Spring은 관리자 페이지를 따로 구성해주지는 않는 것 같다, 고 생각했는데, 그런 비슷한 기능인 Spring Security 프레임워크가 있어서 이번 기회에 확인해볼까 한다.  
&nbsp;&nbsp;&nbsp;&nbsp;Spring에서는 외부 라이브러리를 추가할 때, 구성한 프로젝트, gradle, maven, Dynamic Web에 따라 방법이 다르다. 나는 지금까지 Dynamic Web Project만 만들었으니, 이 게시글도 Dynamic Web Project 에 초점을 맞춰서 진행할 예정이다. 하지만 알아둘 필요는 있다고 생각이 들기 때문에 자료는 추가적으로 게시글에 첨부할 것이다. 그래봤자 처음 설정할때만 다르게 적용하는 것만 같다.

#### Dynamic Web Project의 경우
&nbsp;&nbsp;&nbsp;&nbsp;Dynamic Web Project는 외부 라이브러리를 lib 폴더에 직접 추가해야 한다. http://mvnrepository.com/에서 아래에 해당하는 라이브러리 호환성을 확인 후에 다운받는다.  

>>1. spring-security-web  
>>2. spring-sercurity-core  
>>3. spring-security-config  

&nbsp;&nbsp;&nbsp;&nbsp;그 후에 web.xml에 해당 Spring Security를 사용하기 위해 filter를 작성해야 한다. Spring Security는 DispathcerServlet을 통과하기 이전인 filter에서 보안을 검증할 수 있는 것 같다. web.xml 내부 태그를 나열하는 것은 이전 게시글에 있으니 의미가 없을 것 같다. 여기서는 추가된 태그만 보여주어도 충분할 것 같다.  

```xml

... 생략

<context-param>
	<param-name>contextConfigLocation</param-name> 
	<param-value>/WEB-INF/spring/*.xml</param-value>
 </context-param> 
 
 <listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class> 
 </listener>

... 생략

  <filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

&nbsp;&nbsp;&nbsp;&nbsp;이전과 다르게 Root 설정파일이 필요한 것 같다. 그 전까지는 Root 설정파일 없이 Servlet 설정파일 하나로 프로젝트를 했었는데, 오류가 발생한다.  따라서 상단에 `ContextLoaderListener` listener 클래스를 등록하고 context-param으로 이름과 위치를 지정한다. 위치는 `/WEB-INF/spring/*.xml`으로 지정해놓았는데, `*`을 사용하는 것 보다는 구체적으로 기재하는게 더 나을 것 같다. 일단은 테스트 겸 해서 `*.xml`로 지정해놓았다. 해당 경로에 root-context.xml을 생성하는데, `bean`, `c`, `context`, `lang`, `mvc`, `p`, `tx` 를 추가해서 xml 파일을 만들었다. 사실 이렇게 스키마 파일을 집어넣지 않아도 될 것 같긴 한데, 적어도 `bean`은 있어야 할 것 같다. 사용할 스키마 파일만 정의하고 xml 내용은 현재 비어있다.
그리고 `<filter>`를 정의한다. 위와 같이 정의해야 Spring Security를 사용할 수 있다. 하나 눈여겨봐야 할 점은 `<url-pattern>`인데, 이전 `spring-servlet.xml`에서 정의한 `url-pattern`과 동일한 태그지만, 조금 차이가 있다. `spring-servlet.xml`의 `<url-pattern>`은 확장자를 정의하는 것인데, Spring Security에서는 내부적으로 작업을 위해 사용되는 URL이 있는데, 이 URL들이 뒤에 확장자가 붙는 스타일이 아니라서 확장자로 패턴을 줄 경우에는 로그인이 정상적으로 이루어지지 않는다.  
&nbsp;&nbsp;&nbsp;&nbsp;이렇게 Root 설정 파일과 `web.xml`파일을 설정한 뒤, Sprint Security 설정 파일을 생성해야 한다. 경로는 /WEB-INF/spring/*.xml이다. 나는 `spring-security.xml`이라고 지었다. 해당 설정 파일을 Root 설정 파일과 같은 경로에 두고 `spring-security.xml`을 세팅해야 한다. 가장 기본적인 설정 방식이라고 한다.

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

&nbsp;&nbsp;&nbsp;&nbsp;하나하나 설정파일은 분석해야 할 필요성이 있다고 생각이 들기는 하지만, 이 게시글의 학습목표는 어디까지나 적용 후에 로그인 페이지를 확인하는 것이기 때문에 다음으로 미룬다. 아마 다음 게시글에 상단에 있는 `web.xml`과 함께 하나씩 얕게나마 분석을 할 것 같다.  
&nbsp;&nbsp;&nbsp;&nbsp;이렇게 하면 설정은 끝이난다. 이제 eclipse를 통해 tomcat8를 실행하고 웹 브라우저에 접근 가능한 URL 주소(ex - localhost:8088/springSecurity/index.html)을 입력하면   

![spring_security_login]({{"assets/img/spring_security/spring_security_loginpage.png" | relative_url }})  
*로그인 페이지가 보인다.*

&nbsp;&nbsp;&nbsp;&nbsp;해당 페이지에서 id/pw를 아무거나 입력하면 실패하며, `spring_security.xml`에 등록된 `<user-service>`에 등록된 사용자만 가능하다. 이것도 권한 문제가 있어서 사용자는 관리자 페이지로 들어갈 수 없고, 관리자는 사용자 페이지에 들어갈 수 없다.  
&nbsp;&nbsp;&nbsp;&nbsp;이렇게 하면 일단 기본적인 Spring Security를 적용하는 것은 완료되었다. 다음 게시글에는 설정 파일을 분석하는 시간을 가져봐야 겠다.

<hr/>
L : Spring Security를 Dynamic Web Project에 적용할 수 있게 되었다.

<hr/>

_참고문헌_

제타건남님의 스프링 시큐리티 : <https://zgundam.tistory.com/44?category=430446>
