---
title: Spring MVC - 환경설정 파악하기 - 01
category:
- Java
tag:
- Spring MVC
---

&nbsp;&nbsp;&nbsp;&nbsp;K : Model2 + MVC를 가지고 간단한 게시판을 만들 수 있다. 하지만 내부 구조나 Servlet 컨테이터, 라이프 사이클은 모른다.   
&nbsp;&nbsp;&nbsp;&nbsp;W : Spring에서는 기존 Model2 + MVC의 환경설정파일과 Spring의 환경설정 파일을 체크해서 추상적인 Spring 컨테이너 구동 원리를 이해하고 싶다.

## 학습목표
- Model2 + MVC 패턴에서 사용한 web.xml 파일을 파악한다.
<hr/>

&nbsp;&nbsp;&nbsp;&nbsp;이전에 학습해서 Model2 + MVC 패턴으로 만든 Servlet의 환경설정 먼저 보면 다음과 같다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
        http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
        id="testServlet" version="3.1">
  <display-name>testServlet</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
  </welcome-file-list>
</web-app>
```
&nbsp;&nbsp;&nbsp;&nbsp;이클립스에서 처음 Dynamic Web Project와 web.xml을 생성하면 다음과 같이 설정되며, `<web-app>` 태그의 id 속성값만 기본값이 되어 있다. id는 프로젝트 명으로 변경해서 사용하기를 권장하고 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;따로 특기할만한 것은 없다. `<welcome-file-list>`는 서버의 루트 주소로 접근했을 경우에 가장 처음에 띄워줄 경로를 설정하는 태그이다. 기본값으로 index 혹은 default가 표기된다.  
<hr/>
_현재 web.xml에 대해 알고 있는 지식은 이 정도이다. 그렇다면 web-app 내부에 정의되어 있는 저 이상한 요소들은 뭘까?_
<hr/>
&nbsp;&nbsp;&nbsp;&nbsp;`<web-app>`내부에 있는 스키마 설정은 XML Schema로 Deployment Descriptor(배포 서술자, DD)라고 한다. 깊이 이해하기에는 생소한 개념이라 간단하게 어떤 역할을 하는지 정도만 확인하기로 했다.  
&nbsp;&nbsp;&nbsp;&nbsp;**xmls** : 기본 네임 스페이스를 선언하는 것이다. 이 문서에 사용되는 모든 요소들은 해당 네임 스페이스에 정의되어 있다. Java의 Package와 비슷한 개념으로 이해할 수 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;**xmls:xsi** : 또한 네임스페이스이다. 단지 ":xsi"를 덧붙여서 해당 네임 스페이스의 속성을 부여한다. 아직 이해가 되지 않아 정확히 어떤 의미를 가지는지는 모르겠다. XML Schema Instance로 이 네임스페이스를 가져오면 xsi:schemaLoaction 속성을 사용할 수 있게 된다고 한다.  
&nbsp;&nbsp;&nbsp;&nbsp;**xsi:schemaLocation** : 두 개의 값이 들어가며 널 구분자로 구분한다. 첫번째값은 사용할 네임 스페이스, 두 번재는 해당 네임 스페이스에서 사용하고자 하는 XML 스키마의 위치를 가리킨다. 외부 XMl 스키마의 위치로 위와 같은 URL로 접속하면 외부 XML 스키마를 확인할 수 있다. 해당 외부 XML 스키마에는 Spring에서 정의할 `<Servlet>` 관련 태그가 정의되어 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;낯선 부분이라 이해하는데 시간이 많이 소요되었다. 그리고 정확하게 이해할 수도 없어서 다시금 확인하고 해당 내용을 수정해야 할 것 같다. 하지만 적어도 `<web-app>`의 DD가 어떤 역할을 하는지 알게 되었다.  

&nbsp;&nbsp;&nbsp;&nbsp;L : XML에서 DD의 역할 및 필요 자원을 참조하는 방법(보강 필요)


<hr/>

_참고문헌_  
고양이.꼬리 && 개.발 : <https://kittytail38.tistory.com/entry/Deployment-Descriptor-webapp%EC%9D%98-%EC%86%8D%EC%84%B1>  
공간정보와 인터넷지도 : <https://www.internetmap.kr/entry/XML-Schema-Tutorial>  
TCPschool.com : <http://www.tcpschool.com/xml/xml_basic_namespace>
