---
title: Spring MVC -  HandlerMapping
category:
- Java
tag:
- Spring MVC
---

<hr>

&nbsp;&nbsp;&nbsp;&nbsp;W : Spring에서는 기존 Model2 + MVC의 환경설정파일과 Spring의 환경설정 파일을 체크해서 추상적인 Spring 컨테이너 구동 원리를 이해하고 싶다.

## 학습목표
&nbsp;&nbsp;&nbsp;&nbsp;- HandlerMapping의 기능과 종류에 대해서 알 수 있다.

<hr/>

&nbsp;&nbsp;&nbsp;&nbsp;어제는 학원 갔다 오는게 너무 피곤해서 올릴 체력이 없었다. 어제 올릴 계획이었는데 전부 뒤로 미루고 집에 오자마자 바로 누웠던 것 같다. 학원에서 이전 수강생들의 프로젝트를 보고 있다가 `Interceptor`라는 생소한 개념이 나왔다. 앞으로 프로젝트를 할 때 필요할 것 같아 `Interceptor`에 대해 약간이라도 공부를 해놓아야 할 것 같아서 살펴보니, 이전에 `HandlerMapping`나 `HandlerAdapter`와 같은 내용들을 거의 건너뛰어서 이해를 했었는데, 이제는 어느정도 구체적으로 웹 요청에서 어떤 흐름을 타는지 이해해야 할 필요가 있을 것 같아서 조금 더 자세하거 알아보기로 했다.

#### `HandlerMapping`
&nbsp;&nbsp;&nbsp;&nbsp;`HandlerMapping`은 HTTP 요청정보를 이용해서 컨트롤러를 찾아주는 역할이다. `DispatcherServlet`은 등록된 `HandlerMapping` 전략들에서 `HttpServletRequest`를 전달하면서 매칭되는 객체를 찾는다. 스프링에서 제공하는 핸들러 매핑 전략을 총 5가지가 있다.

##### `BeanNameUrlHandlerMapping`
&nbsp;&nbsp;&nbsp;&nbsp;`BeanNameUrlHandlerMapping`은 HTTP 요청 URL과 Bean의 이름을 비교하여 일치하는 Bean을 찾아준다. XML에 해당 URL 주소와 `Controller`를 직접 매핑시킨다.

```xml
<!-- hello로 시작하면 모두 여기 매핑된다 -->
<bean name="/hello*" class="HelloController" />

<!-- **는 하나 이상의 경로를 매핑 할 수 있다 -->
<bean name="/root/**/sub" class="SubController" />
```

##### 'ControllerBeanNameHandlerMapping`
&nbsp;&nbsp;&nbsp;&nbsp;`BeanNameUrlHandlerMapping`과 유사하지만 Bean 이름을 URL로 짓지 않아도 된다는 점이 다르다. Bean의 name에 자동으로 `/`이 붙여진 상태로 URL이 만들어지고, 그 URL을 가지고 `Controller`에 매핑한다.

```xml
<bean name="hello" class="HelloController" /> <!-- /hello로 변경되어 URL 매핑 -->
```

##### `ControllerClassNameHandlerMapping`
&nbsp;&nbsp;&nbsp;&nbsp;Bean의 클래스 이름을 URL에 매핑해주는 매핑 클래스이다. 위의 xml 태그와는 다르게 자바 클래스로 매핑하며 기본적으로 클래스의 이름을 가져오나, 클래스 이름에 `Controller`가 들어가면 `Controller`를 제외한 클래스 이름을 URL로 매핑하며, Spring이 전부 소문자로 변경하여 URL 매핑을 하게 된다.

```xml
<beans ..>
   ...
   <bean class="org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping"/>
    <!-- 이 경우 URL은 employeeList -->
   <bean class="com.easycompany.controller.hierarchy.EmployeeListController"/>
    ...
</beans>
```

##### `SimpleUrlHandlerMapping`
&nbsp;&nbsp;&nbsp;&nbsp;`SimpleUrlHandlerMapping`는 Ant-Style 패턴 매칭을 지원하며, 하나의 `Controller`에 여러 URL을 매핑할 수 있다. 

```xml
         <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
                <property name="mappings">
                        <props>
                    
                                <prop key="/updateEmployee.do">updateEmployeeController</prop>
                                <!-- 여러 URL이 하나의 Controller로 매핑 -->
                                <prop key="/**/login.do">staticPageController</prop>
                                <prop key="/static/*.html">staticPageController</prop>
                        </props>
                </property>
         </bean>
         <bean id="updateEmployeeController" class="com.easycompany.controller.hierarchy.UpdateEmployeeController" />
         <bean id="staticPageController" class="org.springframework.web.servlet.mvc.UrlFilenameViewController" />
```

##### `DefaultAnnotationHandlerMapping`
&nbsp;&nbsp;&nbsp;&nbsp;`@RequestMapping` 이라는 어노테이션을 이용해 매핑을 선택한다. `@MVC`를 개발하려면 `RequestMappingHandlerMapping`을 사용해야 한다. 단 jdk 1.5 이상 환경에서 동작한다.  
&nbsp;&nbsp;&nbsp;&nbsp;`RequestMappingHandlerMapping`는 3가지 사용 방법이 있다.

- 선언하지 않는 방법
- `<mvc:annotation-driven/>`을 선언하는 방법
- `RequestMappingHandlerMapping`을 직접 선언하는 방법

&nbsp;&nbsp;&nbsp;&nbsp;선언하지 않는 방법은 `RequestMappingHandlerMapping`을 Bean으로 선언하지 않고, `spring-servlet.xml에` `<context-component-scan base-package="">`를 사용한다. 해당 태그를 xml에 기재하면 base-package 하위의 @Controller 중에서 @RequestMapping이 선언된 메서드와 URL을 매핑시켜준다.  
&nbsp;&nbsp;&nbsp;&nbsp;`<mvc:annotation-driven/>`은 이전에 다루어보았다. 내부에 `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping`, `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter`이 구성되어 `@Controller`의 `@RequestMapping`와 매핑된다.  
&nbsp;&nbsp;&nbsp;&nbsp;`RequestMappingHandlerMapping`을 직접 선언하는 방법은 xml에 직접 `RequestMappingHandlerMapping`을 선언한다. 

```xml
 <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
  <!-- 생략 -->
 ```

&nbsp;&nbsp;&nbsp;&nbsp;다른 핸들러 매핑과 함께 사용할때 선언한다고 한다. `@RequestMapping` 이외에 다른 매핑과 혼용해서 사용할 때 사용하는 것 같다.

&nbsp;&nbsp;&nbsp;&nbsp; L : 핸들러 매핑에 대해 알게 되었다.

<hr>

_참고문헌_

전자정부 프레임워크 : <https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte2:ptl:handlermapping>  
JunYoung Park님 블로그 : <https://joont92.github.io/spring/HandlerMapping-HandlerAdapter-HandlerInterceptor/>