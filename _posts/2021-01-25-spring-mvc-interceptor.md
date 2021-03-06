---
title: Spring MVC -  Interceptor
category:
- Java
tag:
- Spring MVC
---

<hr>

&nbsp;&nbsp;&nbsp;&nbsp;W : Spring에서는 기존 Model2 + MVC의 환경설정파일과 Spring의 환경설정 파일을 체크해서 추상적인 Spring 컨테이너 구동 원리를 이해하고 싶다.

## 학습목표
- Interceptor의 기능과 구성에 대해서 알 수 있다.

<hr>

&nbsp;&nbsp;&nbsp;&nbsp;HandelerMapping과 HandlerApdater에 대해서 지난 시간에 살펴보았다. 이 두가지 기능에 대해서는 게시글을 작성하기 전부터 어떤 역할을 하는지는 알았으나, 내부 구성이 어떻게 이루어져 있는지에 대해서는 확인하지 않고 넘어갔었다. 이번에 조금 더 깊게 두 가지 기능에 대해서 알게 되었다고 생각한다.  
&nbsp;&nbsp;&nbsp;&nbsp;하지만 이 Interceptor에 대해서는 알고 있는게 없다. 이전 학원 수강생들의 프로젝트 자료를 통해서 지난주에 접해본게 다였다. 도식화 되어 있는 자료를 통해서 대충 어느 위치에서 실행이 되고 있는지만 알고 있었기 때문에 정리가 필요한 부분이었고, 오늘에서야 인터넷을 뒤져서 어느정도 어설프게 접근을 해보았다.  
&nbsp;&nbsp;&nbsp;&nbsp;Interceptor는 중간에 요청을 가로채서 어떤 기능을 하는 것을 말한다. 정식 명칭은 Handler Interceptor이다. 이전 전자정부 프레임워크 참고문헌에서 Spring의 요청 흐름에서 DispatcherServlet이 어떻게 진행되는지를 이미지로 본 적이 있다. 당시에 확인할 수 있었던 것은 HandlerAdaptor 와 HandlerMapping 사이에 Interceptor 가 껴있는 정도였다. 즉, 두 기능 사이에서 요청을 가로채서 특정 기능을 수행하는 것을 알 수 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;자료를 다양하고 전문적으로 찾아보지는 못했지만, 인터넷에서 공통적으로 말하는 주요 기능은 `인증과 권한`에 대한 처리를 하기 위해 Interceptor를 사용한다고 한다. 이젠 Model2 + MVC에서는 세션을 처리할 때 JSP 파일에서 session 객체에 getAttribute()를 사용해서 세션을 가져왔다. 회원 인증이 필요 없는 사이트라면 세션에 대해서 크게 신경쓸 필요가 없겠지만, 회원 인증 여부를 필수적으로 체크해야 하는 사이트의 경우에 위 방법대로 한다면 페이지마다 세션을 체크해야 한다. 매우 비효율적인 방법이다. 조금 더 나은 방법이라면 컨트롤러 클래스에서 세션을 체크하는 방법도 있는데, 이것도 양이 줄어들 뿐이지 근본적인 문제는 해결되지 않는다.  
&nbsp;&nbsp;&nbsp;&nbsp;이러한 경우에 세션 처리를 쉽게하기 위해 Spring은 Interceptor를 제공한다. Interceptor는 DispatcherServlet에서 HandlerMapping으로 적절한 컨트롤러 클래스를 찾은 다음에 요청을 가로챌 수 있다. 컨트롤러 클래스야 Servlet 컨테이너에 대량으로 만들 수 있기 때문에 비효율적이지만, Servlet 컨테이너는 JSP나 컨트롤러처럼 대량으로 찍어낼 이유가 없기 때문에 훨씬 효율적으로 세션을 관리할 수 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;또한, 권한의 경우에는 컨트롤러에서 뷰로 렌더링하기 전에 추가 작업을 할 수도 있다. 웹 페이지가 권한에 따라서 GNB(Global Navigation Bar)이 항목이 다르게 노출되어야 할 때 등의 처리를 하기 좋다  
&nbsp;&nbsp;&nbsp;&nbsp;그러면 이제 Interceptor의 내부 구조에 대해서 확인해보겠다.

```java
public interface HandlerInterceptor {
  boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler);
  void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView mav);
  void afterCompletion(HttpServletRequest request, HttpServeletResponse response, Object handler, Exception ex);
}

```

&nbsp;&nbsp;&nbsp;&nbsp;Interceptor를 만들려면 HandlerInterceptorAdapter 클래스를 상속거나 HandlerInterceptor 인터페이스를 구현하면 된다. 하위 메서드는 3가지로 구성되어 있다.  

##### `preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)`
&nbsp;&nbsp;&nbsp;&nbsp;이 함수는 클라이언트에서 컨트롤러로 요청이 들어올 때 가로채는 것이다. 컨트롤러가 호출되기 전에 실행되는 메서드로 위에서 말했던 것처럼 세션을 체크해기에 용이하다. 세션이 없다면 로그인 페이지로 돌려보내고, 있다면 컨트롤러를 실행하면 별 수고로움 없이 인증을 관리할 수 있을 것 같다. 이후에는 일반적인 세션 체크 로직을 만들면 된다. 리턴값으로는 true/false를 반환하며, true이면 컨트롤러로 넘겨주지만 false면 중단하고 남은 Interceptor와 컨트롤러는 실행되지 않는다. 해당 로직을 구현하면 아래와 같은 코드가 될 수 있다.

```java
   @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        HttpSession session = request.getSession();
        UserVO loginVO = (UserVO) session.getAttribute("loginUser");
 
        if(ObjectUtils.isEmpty(loginVO)){
            response.sendRedirect("/moveLogin.go");
            return false;
        }else{
            session.setMaxInactiveInterval(30*60);
            return true;
        }
        
    }
```

##### `postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView mav)`
&nbsp;&nbsp;&nbsp;&nbsp;이 함수는 컨트롤러가 실행된 후에 호출되는 메서드이다. 이 부분에 대해서는 어떻게 활용하는지 자료를 찾지 못했다. 해당 메서드에 대한 활용 자료는 혹시 나중에 찾게 된다면 첨부하도록 하겠다. 다만 컨트롤러가 돌려준 ModelAndView 타입의 정보가 제공되어 컨트롤러 작업 결과를 참조하거나 조작할 수 있다고 하니, 위에 있는 권한과 관련하여 페이지에서 노출과 제한을 조작할 수 있을 것 같다고 생각한다.  

##### `afterCompletion(HttpServletRequest request, HttpServeletResponse response, Object handler, Exception ex)`
&nbsp;&nbsp;&nbsp;&nbsp;이 함수는 뷰에서 최종 결과를 생성하는 일을 포함한 모든 일을 완료되었을 때 실행되는 메서드이다. 무슨 일을 하는지, 그리고 왜 필요한지 아직 이해하지 못했다. 요청 처리중에 사용한 리소스를 반환해주기 적당하다고 하는데, 체감되지 않는다.  

&nbsp;&nbsp;&nbsp;&nbsp;이렇게 Interceptor의 내부 구성을 알아보았다. 다른 것들과 마찬가지로 XML 파일에 bean으로 등록해야 한다. DispatcherServlet이 읽는 xml 설정 파일에 아래 코드를 추가하면 된다. 관습적으로 spring-servlet.xml을 사용했다. 작성 코드는 조금 다를 수 있으나 기본 경로와 계층 구조는 동일하다고 생각된다.
```xml
<mvc:interceptors>
       <mvc:interceptor>
                <!-- 요청 패스 설정 -->
              <mvc:mapping path="/**"/>
              <!-- Interceptor 클래스 설정 설정 -->
              <beans:bean class="com.sunghyun.test.interceptor.TestInterceptor"></beans:bean>
       </mvc:interceptor>
</mvc:interceptors>
```

L : Interceptor에 대해서 알게 되었다.

<hr>

_참고문헌_

victolee님의 블로그 : <https://victorydntmd.tistory.com/176?category=698080>  
#에게님의 블로그 : <https://to-dy.tistory.com/21>  
TOASTMeetUP! 게시글 : <https://meetup.toast.com/posts/151>  
Rednics Blog님의 블로그 : <https://springsource.tistory.com/4>  