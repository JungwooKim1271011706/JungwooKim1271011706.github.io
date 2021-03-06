---
title: Spring MVC -  HandlerAdapter
category:
- Java
tag:
- Spring MVC
---

<hr>

&nbsp;&nbsp;&nbsp;&nbsp;W : Spring에서는 기존 Model2 + MVC의 환경설정파일과 Spring의 환경설정 파일을 체크해서 추상적인 Spring 컨테이너 구동 원리를 이해하고 싶다.

## 학습목표
&nbsp;&nbsp;&nbsp;&nbsp;- HandlerAdapter의 기능과 종류에 대해서 알 수 있다.

<hr/>

&nbsp;&nbsp;&nbsp;&nbsp;지난시간에는 `HandlerMapping`에 대해서 알아보았다. `HandlerMapping`은 요청 정보를 가지고 적절한 `Controller`를 찾아주는 역할을 한다. `HandlerAdapter`는 찾아낸 `Controller` 내부에 적절한 메서드를 호출하는 역할을 한다.
Spring MVC에서 지원하는 `Controller`의 종류는 4가지이기 때문에, 내부 메서드를 호출해야 하는 `HandlerAdapter`또한 4가지가 있다. 이 중에서 `SimpleServletHandlerAdapter`를 제외한 3가지 `HandlerAdapter`는 `DispatcherServlet`에 기본 전략으로 설정되어 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;`HandlerAdapter`의 종류는 다음과 같다.

<hr>

>>1. SimpleServletHandlerAdapter
>>2. HttpRequestHandlerAdapter
>>3. SimpleControllerHandlerAdapter
>>4. RequestMappingHandlerAdapter

<hr>

##### `SimpleServletHandlerAdapter`
&nbsp;&nbsp;&nbsp;&nbsp;`SimpleServletHandlerAdapter`는 표준 `Servlet Interface`인 `javax.servlet.Servlet`을 구현한 Servlet Class를 Spring MVC의 `Controller`로 사용할 수 있다. 이 방식은 Servlet Class 코드를 그대로 유지한 채 Spring의 Bean을 등록하여 사용이 가능하다. 그러나 이 경우에는 Servlet Lifecycle이 관리되지 않아 init(), destroy() 메서드는 실행되지 않는다. Servlet 초기화 작업을 해야 한다면 Bean에 `init-method` attribute나 `@PostConstruct` 등을 이용해서 Bean 생성 이후에 초기화 메서드를 실행되게 해야 한다. 라고 하는데, 일반적인 경우에는 사용하지 않을 것 같다. 아마 기존 Spring에서 Servlet을 연동할 경우에만 사용하게 되지 않을까 싶다.  
&nbsp;&nbsp;&nbsp;&nbsp;기본 전략이 아니기때문에 물론 Bean에 등록을 해줘야 한다.
```xml
<bean class="org.springframework.web.servlet.handler.SimpleServletHandlerAdapter"/>
```
&nbsp;&nbsp;&nbsp;&nbsp;`Controller`는 Servlet을 생성할 때처럼 생성해주고 `HttpServlet`을 상속받으면 `SimpleServletHandlerAdapter`가 찾아준다.
```java
public class AccountController extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    }
}

```

##### `HttpRequestHandlerAdapter`
&nbsp;&nbsp;&nbsp;&nbsp;`HttpRequestHandler` 인터페이스를 구현한 클래스를 `Controller`로 사용할 경우에 사용되는 `Adapter`이다.
```java
public interface HttpRequestHandler{
    void handleRequest(HttpServletRequest request, HttpServletResponse resposne) throws ServletException, IOException;
}
```
&nbsp;&nbsp;&nbsp;&nbsp;Servlet 인터페이스와 생김새가 유사하다. Servlet처럼 동작하는 `Controller`를 만들기 위해서 사용한다고 하는데, HTTP 기반의 RMI와 같은 Low level 서비스를 개발할때 사용한다고 하는데, 감이 오질 않는다. `Serlvet`보다 조금 더 러프하게 다뤄질때 사용하는 것 같은데...  

##### `SimpleControllerHandlerAdapter`
&nbsp;&nbsp;&nbsp;&nbsp;`SimpleControllerHandlerAdapter`는 `Controller` 인터페이스를 통해 구현해서 만든다. Spring MVC의 대표적인 컨트롤러 타입으로 볼 수 있다. `Spring 3.0`에 `Annotation`을 이용한 `Controller` 매핑이 등장하기 전까지는 가장 많이 사용한 컨트롤러 타입이었다.  
&nbsp;&nbsp;&nbsp;&nbsp;아래와 같은 인터페이스를 구현해서 만든다고는 하는데, 실제로는 권장하지 않고, `Controller` 인터페이스를 미리 구현한 간단한 클래스인 `AbstractController`를 상속해서 `Controller`를 만드는 기술이 사용되었다. `AbstractController`에는 웹 브라우저를 클라이언트로 갖는 `Controller`의 최소한의 기능이 구현되어 있다고 한다.
```java
public interface Controller {
 
    ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception;
}
```

##### `RequestMappingHandlerAdapter`
&nbsp;&nbsp;&nbsp;&nbsp;Spring 3.0에서 3.1로 올라가면서 기존에 있던 `AnnotationMethodHandlerAdapter`가 `RequestMappingHandlerAdapter`로 변경되었다. 자세한 변경 내용은 이 게시글의 목표를 벗어나기 때문에 따로 기재하지는 않는다. 이 `Adapter`는 `RequestMappingHandlerMapping`과 대응되는 클래스로, `RequestMappingHandlerMapping`를 사용하기 위해서는 `RequestMappingHandlerAdapter`를 이용해야만 한다.  
&nbsp;&nbsp;&nbsp;&nbsp;다른 `Handler`와는 다르게 `Controller`의 타입이 정해져 있지 않다. Servlet, Controller, HttpRequestHandler 등의 타입이 고정되어 있지 않다. 대신 `Class`와 메서드에 붙은 몇가지 `Annotation` 정보와 메서드 이름, 파라미터, 리턴 타입에 대한 규칙 등을 종합적을으로 분석해서 컨트롤러를 결정하고 호출한다. 또한, 다른 `Handler`의 경우는 URL 당 하나의 컨트롤러가 매핑되나, `RequestMappingHandlerAdapter`는 `Annotation`으로 하위 해당 컨트롤러 내부의 하위 메서드를 연결할 수 있기 때문에 하나의 컨트롤러에 여러 URL이 매핑될 수 있다.
```java
@Controller
public class testController{

    @RequestMapping(value="test")
    public String test(){
        /...
    }
}
```

&nbsp;&nbsp;&nbsp;&nbsp;L : `HandlerAdapter`에 대해서 알게 되었다.

<hr>

_참고문헌_

Rednics Blog의 글 :  <https://springsource.tistory.com/2>  
JunYoung Park의 블로그 : <https://joont92.github.io/spring/HandlerMapping-HandlerAdapter-HandlerInterceptor/>  
Son SangWoo의 블로그 : <https://rerewww.github.io/spring/spring-handlerAdapter/>  
머루의 개발블로그 : <http://wonwoo.ml/index.php/post/2308>  
