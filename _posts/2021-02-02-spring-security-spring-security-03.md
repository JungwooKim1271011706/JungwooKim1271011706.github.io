---
title: Spring Security - 03
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
>>8. Spring Security-core 4.2.1 >> 참조할 게시글을 못찾아서 버전을 변경했다.
>>9. Spring Security-config 4.2.1 >> 참조할 게시글을 못찾아서 버전을 변경했다.
>>10. Spring security-web 4.2.1 >> 참조할 게시글을 못찾아서 버전을 변경했다.
 
<hr/>

&nbsp;&nbsp;&nbsp;&nbsp;W : Spring Security 를 활용하여 보안을 향상시키는 기술 습득

## 학습목표
- maven 프로젝트에서 Spring Security 적용하기

&nbsp;&nbsp;&nbsp;&nbsp;오늘은 지난번에 말한 대로 Spring Security를 Dynamic Web Project 외에 maven, gradle에서 적용하는 방법에 대해 알아보겠다고 했는데, 게시글을 쓰고 보니까 Maven은 문제가 없을지 몰라도 Gradle은 xml 파일을 사용하지 않고 자바 파일로 설정을 하는 것 같다. 당연히 한번도 해본적이 없기 때문에 그 과정은 집어넣지 않았다. 이 게시글에서는 Maven을 통한 설정만을 기재하고 조금 더 학습을 한 다음에 Gradle Project에 Spring Security를 적용하는 게시글을 기재하기로 하겠다. 그렇게 긴 글이 될 것 같지는 않다. Dynamic Web Project와 비교해서 차이점만 간단하게 짚고 넘어가도 훑어보는데는 무리가 없을 것 같다. 동일한 과정은 생략한다.  

#### Maven Project에 Spring Security 적용하기

1. 의존성 등록하기(외부 라이브러리 다운받기)
&nbsp;&nbsp;&nbsp;&nbsp;Maven에서는 `pom.xml`에 의존성을 등록한다. 여기저기 웹서핑을 하면서 얼핏 본 것으로 Maven에서는 Spring을 동작시키는데 필요한 외부 라이브러리를 자동으로 받아온다고 한다. Spring Security를 동작하는데 필요한 외부 라이브러리를 pom.xml에 정의하고 다운받는 과정이다. 

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>4.2.1.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>4.2.1.RELEASE</version>
</dependency>
```
&nbsp;&nbsp;&nbsp;&nbsp;spring security web이랑 spring security config만 받아왔다. 현재 진행중인 Spring Security에서는 spring security core도 받아왔었는데, 필수적인 요소는 아니었나보다.

2. `web.xml`에 `<filter>` 등록 -> 생략

3. `web.xml`에 `spring_security.xml`을 로딩하도록 설정 -> 생략

4. `spring_security`에 필요한 태그 설정(http, authentication-manager ...) -> 생략

&nbsp;&nbsp;&nbsp;&nbsp;원래 Gradle Project까지 진행하려고 했었는데, 급하게 빼는 바람에 굉장히 게시글이 허전해졌다. 하지만 뭐 누구에게 보여주려고 쓰는 것도 아니고 그렇다고 `build.gradle`에 의존성만 추가하는 걸로 마무리 하는 건 어중간해서 더 질 나쁜 게시글이 될 것 같다. Maven Project에 Spring Security를 설정하는 것은 이것으로 마무리 짓고, 로그 기록을 분석하는 것으로 게시글을 끝내고자 한다.  

#### 로그 분석
&nbsp;&nbsp;&nbsp;&nbsp;나는 Spring 초기 게시글에 Spring 컨테이너 동작시에 로그가 어떻게 찍히는지 확인해보았다. 그 당시에는 `root-context.xml`도 없었고 `spring-security.xml`도 없었다. 2개나 늘어났으며, 이전부터 궁금하던게 `root-context.xml`이 없을 때에는 `spring-servlet.xml`이 루트 게층에서 설정 파일로서 동작하는데, `root-context.xml`이 있으면 로그가 어떻게 찍힐까 궁금했고 언젠가는 이 궁금증을 해소해야겠다고 생각하고 있었다. 마침 Spring Security 프레임워크를 설정하면서 자연스럽게 그 궁금증이 해소되었다.  

```java

// 기존 프로젝트 >> spring-servlet.xml로 로딩---------------------------------
정보: Initializing Spring FrameworkServlet 'spring'
[2021-02-02 00:57:57,093] [INFO] (FrameworkServlet.java:457)     - FrameworkServlet 'spring': initialization started
[2021-02-02 00:57:57,121] [INFO] (AbstractApplicationContext.java:510)     - Refreshing WebApplicationContext for namespace 'spring-servlet': startup date [Tue Feb 02 00:57:57 KST 2021]; root of context hierarchy
[2021-02-02 00:57:57,176] [INFO] (XmlBeanDefinitionReader.java:315)     - Loading XML bean definitions from ServletContext resource [/WEB-INF/framework/servlet-context.xml] // 1. servelt-context.xml 로딩
[2021-02-02 00:57:57,304] [INFO] (XmlBeanDefinitionReader.java:315)     - Loading XML bean definitions from ServletContext resource [/WEB-INF/framework/spring-servlet.xml] // 2. spring-servlet.xml 로딩

// root xml 로딩--------------------------------------------------------------
정보: Initializing Spring root WebApplicationContext
[2021-02-02 00:58:00,801] [INFO] (ContextLoader.java:285)     - Root WebApplicationContext: initialization started
[2021-02-02 00:58:00,904] [INFO] (AbstractApplicationContext.java:510)     - Refreshing Root WebApplicationContext: startup date [Tue Feb 02 00:58:00 KST 2021]; root of context hierarchy
[2021-02-02 00:58:00,963] [INFO] (XmlBeanDefinitionReader.java:315)     - Loading XML bean definitions from ServletContext resource [/WEB-INF/spring/spring-security.xml] // 1. spring-security.xml 로딩
[2021-02-02 00:58:01,002] [INFO] (SpringSecurityCoreVersion.java:58)     - You are running with Spring Security Core 3.2.6.RELEASE // >> spring security 외부 라이브러리 동작
[2021-02-02 00:58:01,008] [INFO] (SecurityNamespaceHandler.java:75)     - Spring Security 'config' module version is 3.2.6.RELEASE
[2021-02-02 00:58:01,133] [INFO] (XmlBeanDefinitionReader.java:315)     - Loading XML bean definitions from ServletContext resource [/WEB-INF/spring/root-context.xml] // 2. root-context.xml 로딩
2월 02, 2021 12:58:01 오전 org.apache.catalina.core.ApplicationContext log
정보: Initializing Spring FrameworkServlet 'spring' // >> servlet 컨테이너 초기화
[2021-02-02 00:58:01,526] [INFO] (FrameworkServlet.java:457)     - FrameworkServlet 'spring': initialization started
[2021-02-02 00:58:01,530] [INFO] (AbstractApplicationContext.java:510)     - Refreshing WebApplicationContext for namespace 'spring-servlet': startup date [Tue Feb 02 00:58:01 KST 2021]; parent: Root WebApplicationContext
[2021-02-02 00:58:01,532] [INFO] (XmlBeanDefinitionReader.java:315)     - Loading XML bean definitions from ServletContext resource [/WEB-INF/framework/spring-servlet.xml] // 3. spring-servlet.xml 로딩

```

&nbsp;&nbsp;&nbsp;&nbsp;로그를 통째로 옮기면 너무 길어서 상당부분 잘라냈다. 일단은 xml 설정 파일을 어떤 순서로 불러오는지를 확인하도록 하겠다.  
&nbsp;&nbsp;&nbsp;&nbsp;`root-context.xml`이 없는 경우에는 

>>1. `servlet-context.xml` 로딩
>>2. `spring-servlet.xml` 로딩

&nbsp;&nbsp;&nbsp;&nbsp;순서로 로딩되는 것 같다. 반면 `root-context.xml`이 있는 경우에는

>>1. `spring-security.xml` 로딩
>>2. spring security 외부 라이브러리 동작
>>3. `root-context.xml` 로딩
>>4. servlet 컨테이너 초기화
>>5. `spring-servlet.xml` 로딩

&nbsp;&nbsp;&nbsp;&nbsp;순서로 로딩된다. 자세히보니 root 파일이 로딩이 먼저 안되거나 하는건 파일 이름상 수용하기 어려운 순서인 것 같다. 이에 대해서도 알아볼 필요가 있을 것 같다. 이건 아마 로그 파일을 좀 더 자세히 분석하면서 알게 될 것 같다.

&nbsp;&nbsp;&nbsp;&nbsp;L : Maven Project에 Spring Security을 설정하는 방법을 알게 되었다.

<hr>

_참고문헌_  

OffByOne님의 블로그 : <https://offbyone.tistory.com/88>
