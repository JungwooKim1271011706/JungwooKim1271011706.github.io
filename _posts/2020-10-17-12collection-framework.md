---
title: 자바 - 12(Collection framework - Collection)
category:
- Java
tag:
- Java
---

&nbsp;&nbsp;&nbsp;&nbsp;자바에서는 필요한 자료 구조를 미리 구현하여 java.util 패키지에서 제공하고 있다. 이를 컬렉션 프레임워크(Collection framework)라고 한다. 컬렉션 프레임워크의 구성은 다음과 같다.  



| Collection | -------- | -------- | --------     | Map     |  -------- |
| -------- | -------- || -------- | -------- | -------- |
| List | Set | -------- |HashTable|HashMap|TreeMap
| ArrayLIst     | HashSet     |-------- |Properties
| Vector     | TreeSet     |--------|
| LinkedList | -------- | --------|

    Collection framework는 데이터를 key=value의 형태로 저장한다.
    Collection은 가상의 일련번호를 가지고 있다. 가상의 일련변호가 key가 되고 데이터가 value가 된다.
    Map은 가상의 일련변호가 없다. 사용자가 key 와 value를 전부 지정해줘야만 한다.

&nbsp;&nbsp;&nbsp;&nbsp;다양한 자료구조들이 존재하지만, 주로 사용되는 것은 List의 ArrayList와 Map의 HashMap이다. 먼저 ArrayList는 배열을 공부할때 나왔던 개념이다. Array와는 다르게 데이터를 동적으로 집어넣을 수 있다. 기본적으로 10개의 데이터를 담을 수 있는 용량이나, .add()를 사용해서 추가한다면 추가할 수 있다. Array와 다르게 배열을 복사해서 더 큰 배열에 집어넣을 필요가 없다.    
&nbsp;&nbsp;&nbsp;&nbsp;사용법은 Array와 큰 차이가 없다. ArrayList를 사용하려면 java.util 패키지에서 ArrayList를 import 해줘야 한다. 그 후에 ArrayList의 참조변수를 new로 인스턴스해서 ArrayList를 만들고 ArrayList에서 제공하는 함수들을 활용해서 연산을 진행하면 된다. 다만, 기존 배열과는 다른 점은 데이터 타입을 정하지 않는다. 따라서 어떤 데이터가 들어와도 기본적으로는 담을 수 있다.    

    int i[] = {1,2,3}; >> 자료형이 int이다. int형 데이터만 배열에 담을 수 있다.
    ArrayList at = new ArrayList(); >> 자료형이 없다. 어떤 데이텨 타입이든 add 가능하다.

&nbsp;&nbsp;&nbsp;&nbsp;다양한 데이터를 담을 수 있다는 것은 데이터를 집어넣기는 편리하다. 무척이나 편리하지만 데이터를 호출할 때에는 무척이나 신경써야만 한다. 형 변환을 거친 후에 데이터를 사용해야만 하기 때문이다. 기본적으로 ArrayList에 담긴 경우에 데이터 타입은 Object로 변경된다. 따라서 Object 타입을 형 변환하여 사용자가 사용해야하는 데이터 타입의 변수를 선언한 후 형 변환을 통해 집어넣는다.  

    String s = (String)at.get(0); >> 형 변환을 한 후에 변수에 초기화한다.
    좀 더 상세하게 단계를 분할하면 다음과 같다.
    int i = ((Integer)aList.get(0)).intValue();
    물론 int i = (int)aList.get(0); 으로 사용해도 아무런 문제가 없다. 이렇게 간단하게
    작성해도 JVM이 알아서 형 변환해서 값을 초기화한다.

&nbsp;&nbsp;&nbsp;&nbsp;여러가지 데이터를 집어넣는 일은 사용하기 여간 불편한 것이 아니다. 이에 대한 보완책으로 자바에서는 <>(제네릭)이라고 하는 기능을 지원한다. <>(제네릭)을 사용하면 ArrayList의 데이터 타입을 변수같이 선언해서 사용할 수 있다. 즉, ArrayList에 담을 수 있는 데이터를 제한하는 것이다. 사용법은 다음과 같다. 

 - ArrayList<String> aList = new ArrayList<String>();

&nbsp;&nbsp;&nbsp;&nbsp;이 경우 String만 데이터에 담기기 때문에 위와 같은 상황에서 ArrayList의 데이터를 꺼내려고 할 때 형 변환을 할 필요가 없다.    

 - Stirng s = aList.get(0); >> 형 변환을 하지 않아도 문제 없다.

&nbsp;&nbsp;&nbsp;&nbsp;아마 ArrayList에 담긴 경우에 데이터 타입은 위와 같이 Object일 것이다. Object를 꺼내려고 할 때에는 JVM이 강제로 String으로 형 변환을 대신 해주는 것 같다. 이렇게 <>(제너릭)을 사용하면 데이터를 무분별하게 담을 수 없기 때문에 좀 더 체계적인 관리가 가능할 것 같다. 데이터를 담는 과정에서 조금 더 세심한 분류가 필요하겠지만.  

&nbsp;&nbsp;&nbsp;&nbsp;Vector와 ArrayList의 사용법은 동일하다. 완벽하게 일치한다. 다만 Vector는 동기화를 지원한다. 동기화는 멀티 스레드 환경에서 동시에 이루어지는 자원에 대해 잠금을 수행하는 것을 말한다. 두 개 이상의 스레드가 동시에 실행되면 같은 메모리 공간에 접근하기 때문에 변수의 값이나 메모리 상태에 오류가 생길 수 있다. 이를 방지하기위해 순서를 지정하고 수행하는 것이 바로 동기화의 역할이다.  
&nbsp;&nbsp;&nbsp;&nbsp;즉, 동기화를 사용하는 Vector는 하나씩 자원을 수행하기 때문에 ArrayList보다 수행 속도가 느리다. 따라서 두 작업이 동시에 실행되는 멀티 스레드 환경이 아닌 경우에는 대부분 속도가 빠른 ArrayList를 사용하기를 권장한다. 만약에 동기화가 필요한 경우라면, ArrayList 생성 코드를 사용하면 된다고 한다.   

 - Collections.synchronizedList(new ArrayList<String>());