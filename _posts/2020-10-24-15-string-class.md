---
title: 자바 - 15 (String Class)
category:
- Java
tag:
- Java
---

&nbsp;&nbsp;&nbsp;&nbsp;String을 선언하는 방법은 두가지가 있다.

    String str1 = new String("abc"); // 생성자의 매개변수로 문자 생성 : 힙 메모리에 생성, 새로운 객체
    String str2 = "test"; // 문자열 상수를 가리킴 : 상수풀 : 문자열 상수의 메모리 주소를 가리킴

&nbsp;&nbsp;&nbsp;&nbsp;String 클래스에서 자주 사용하는 함수는 문자열을 쪼개는 함수다. 파이썬에서는 문자열 슬라이스라고 한다.    
&nbsp;&nbsp;&nbsp;&nbsp;String에서 문자열을 쪼개는 기능을 하는 함수는 다음과 같다.

 - .split(String delim) : 구분자(delimiter)로 String 변수의 문자를 분할할 수 있다. 분할한 문자는 []로 호출할 수 있다. delimiter는 1개만 사용 가능하다.  
 ex ) String str[] = sVal.split(","); //이때 sVal은 문자열("abc,def,ghi")이다. >> str[] = [abc, def, ghi]
 String 배열로 쪼개지면 .charAt()으로 char로 쪼갤 수 있다.

 - .StringTokenizer(String str, String delim) : 참조변수를 이용한다. String 변수의 문자를 분할한다. delimiter를 1개 이상 사용할 수 있다.    
 ex ) StringTokenizer st = new StringTokenizer(sVal, "@^");     
  &nbsp;&nbsp;&nbsp;&nbsp;StringTokenizer는 출력을 할 경우에는 Iterator와 유사한 과정을 거친다. .hasMoreTokens()로 내부에 요소가 있는 경우 true 없으면 false를 반환한다. 그리고 .nextToken()으로 cursor를 출력한 후 cursor를 후위증감연산자로 증가시킨다.    

&nbsp;&nbsp;&nbsp;&nbsp;String에서 문자열을 치환하는 경우는 다음과 같다.

  - .replace(char oldChar, char newChar) : oldChar를 newChar로 치환한다.

&nbsp;&nbsp;&nbsp;&nbsp;String은 final char[] 이기 때문에 변경이 불가능하다. 이런 문자열의 특징을 문자열은 불변(immutable)이라고 한다. 따라서 두 개의 문자열을 연결하면, 둘 중 하나의 문자열이 변경되는게 아니라 새로운 문자열이 생성되고 메모리를 잡아먹는다. 이러한 문제를 해결하기 위해서 StringBuffer, StringBuilder 클래스를 사용한다.StringBuffer와 StringBuilder는 char[]로 final이 없기 때문에 두 클래스를 사용하면 char[]배열이 확장되므로 추가 메모리를 발생하지 않는다. 사용법은 다음과 같다.    

    StringBuffer(String str) : 참조변수 사용

    StringBuffer sb new StringBuffer("java");
    sb.append(" and");
    sb.append(" android");
    sb.append(" programming is fun!!!"); >> sb 메모리의 주소는 연산 후에도 변하지 않는다.

 &nbsp;&nbsp;&nbsp;&nbsp;둘 중 아무거나 사용해도 되나, 현업에서는 StringBuffer를 사용한다고 한다. StringBuilder 클래스가 보완된 클래스라고 하는데 큰 체감은 없는것 같다.

 .substring(int start), substring(int start, int end)   
 &nbsp;&nbsp;&nbsp;&nbsp;매개변수 포함 우측 문자열을 반환한다. start, end를 통해 가운데 문자열을 반환할 수도 있다.
ex ) str.substring(1,5) 는 str 문자열의 인덱스 1~4까지의 문자열을 반환시킨다.

 .indexOf(String str)   
 &nbsp;&nbsp;&nbsp;&nbsp;String에서 매개변수가 위치한 인덱스를 반환한다.

.lastIndexOf(String str)   
&nbsp;&nbsp;&nbsp;&nbsp;String에서 매개변수의 우측 인덱스를 반환한다, 고 하는데 차이를 잘 모르겠다. 다만 매개변수가 "."이 아니라 '.'일 경우에 동작하는 것 같다. char가 매개변수로 필요한게 아닐까.

