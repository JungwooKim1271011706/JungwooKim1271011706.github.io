---
title: 자바 - 13(Collection framework - Map)
category:
- Java
tag:
- Java
---

&nbsp;&nbsp;&nbsp;&nbsp;Map은 key=value로 이루어진 데이터 타입을 배열에 저장한다.(엄밀히 말하면 배열이 아니라고 한다. Map은 { }로 표현하는데, 이는 순서를 보장하지 않기 때문에 배열에 속하지 않는다. 배열은 [ ]로 표현한다) 파이썬의 사전형 데이터와 유사하다. 즉, 기본적으로 데이터를 꺼내는 경우는 ArrayList처럼 가상의 일련번호가 존재하지 않는다. key나 value로 데이터를 꺼낼 수 있다.       
&nbsp;&nbsp;&nbsp;&nbsp;Map에서 가장 자주 사용하는 것은 HashMap이다. HashMap의 사용 방법은 다음과 같다.

 - HashMap hm = new HashMap(); // 물론 java.util.HashMap이 import 되어야 한다.

&nbsp;&nbsp;&nbsp;&nbsp;HashMap은 ArrayList와 다르게 .add()로 추가하지 않고 .put(K, V)으로 추가한다. 아마 add가 순서라는 맥락에서 집어넣는데 사용하고,  put이 순서와 상관 없이 집어넣는 맥락으로 쓰이는 것 같다. 이외에 .get(), .size()는 ArrayList, HashMap 동일하게 쓰인다. 다만 .get()에는 index가 아니라 key 값을 넣어야만 한다.
HashMap에서는 동일한 key에 해당하는 value를 집어넣을 경우에는 덮어쓰기가 된다. Key가 고유한 성질때문에 그런 것 같다.    
&nbsp;&nbsp;&nbsp;&nbsp;HashMap도 Object로 변형되어 저장되기 때문에 데이터를 꺼내려고 할 때 형 변환을 해야 한다. 이는 ArrayList와 동일하게 형 변환을 해주면 된다. <>(제너릭)을 사용할 경우에는 형 변환을 할 필요가 없이 변수에 저장이 가능하다. 

&nbsp;&nbsp;&nbsp;&nbsp;여기까지는 ArrayList와 동일하다. 하지만 ArrayList는 가상의 일련번호를 사용한다, 즉 순서가 있고, 그 순서에 따라 값이 저장된다. 하지만 HashMap은 가상의 일련번호를 사용하지 않기 때문에 순서가 없다. 순서가 없기 때문에, 순차적이지 않다. 이는 수많은 데이터를 꺼내려고 할 경우에 문제가 발생한다.    
&nbsp;&nbsp;&nbsp;&nbsp;ArrayList의 경우는 Array와 같이 배열의 길이를 구하고, 배열에 가상의 일련변호 for 문을 통해서 데이터를 순차적으로 꺼내올 수 있다. 하지만 HashMap은 다르다. 가상의 일련번호가 없다. 순서가 없다. 따라서 ArrayList처럼 for 문을 통해서 접근할 수가 없다. 이러한 경우에 데이터를 뽑아내기 위해서 HashMap에서는 Iterator라는 인터페이스를 사용해서 데이터를 꺼낸다.  
&nbsp;&nbsp;&nbsp;&nbsp;먼저, HashMap에 key 혹은 value를 알아야 한다. 알고 있다면 수월하지만 알고 있지 않은 경우에도 물론 데이터를 뽑아낼 수 있다. key와 value를 알아내는 함수는 다음과 같다.   

    hm.keySet() >> HashMap의 Key값을 받아온다.
    hm.Values() >> HashMap의 Value값을 받아온다.
    hm.entrySet() >> HashMap의 Key = Value 값을 받아온다.

&nbsp;&nbsp;&nbsp;&nbsp;이러면 HashMap의 데이터를 받아올 수 있다. 이렇게 받아온 데이터를 Iterator 참조변수에 대입한다.  

    Iterator keys = hm.keySet().iterator();

&nbsp;&nbsp;&nbsp;&nbsp;Iterator의 함수를 사용할 수 있는 keys 변수가 만들어졌다. Iterator 인터페이스의 구성은 다음과 같다.

    public interface Iterator {
        boolean hasNext();
        // hasNext()는 읽어올 요소가 남아 있는지 확인하고 있으면 true 없으면 false를 반환한다.
        Object next();
        // next()는 커서를 다음 요소로 옮기는 역할을 한다.
        void remove();
    }

&nbsp;&nbsp;&nbsp;&nbsp;Iterator의 함수와 while 문을 활용하면 HashMap의 데이터를 순차적으로 꺼낼 수 있다.

    while(keys.hasnext()) // keys가 존재하면
    {
        String key = (String)keys.next();
        //keys의 커서를 다음 요소로 옮긴다. <>(제너릭)이 사용되었으면 형 변환을 할 필요는 없다.
        String value = (String)value.get(key); // ??
        System.out.println(key + " : " + value);
    }

&nbsp;&nbsp;&nbsp;&nbsp;또한 향상된 for문을 이용해서도 가능하다.

    for (String key1 : hm.keySet() )
    // hm.keySet()으로 반환한 Key값을 key1에 대입한다. 배열의 길이만큼 해당 동작을 반복한다.
    {
        String value1 = hm.get(key1);
        System.out.println(key1 + " : " + value1);
    }

&nbsp;&nbsp;&nbsp;&nbsp;keySet() 혹은 value(), entrySet()으로도 전부 출력이 가능하다. 하지만 아직 코드에 대해서 이해가 공부가 덜 된 상태여서, next로 커서를 옮기면 처음 key값은 건너뛰는게 아닌가 생각이 든다. 그리고 for 문에서는 커서에 대한 코드가 들어가지 않더라도 출력이 되는것 같은데, 어떻게 가능한지 좀 더 꼼꼼하게 따져보고 연구해본 다음에 수정 및 추가를 할 예정이다.