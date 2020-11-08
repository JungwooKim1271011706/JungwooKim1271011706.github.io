---
title: 자바 - 23 (Oracle DB Connection)
category:
- Java
tag:
- Java
---

&nbsp;&nbsp;&nbsp;&nbsp;자바에서 외부 패키지를 연결하기 위해서는 전통적으로 외부 패키지를 classes 폴더에 넣어둔 다음에 classpath를 잡아서 외부 패키지를 사용한다. 자바는 최소단위가 클래스 단위이기 때문에 외부 패키지도 클래스로 구성되어 있다. 다양한 클래스들 중에서 사용자가 필요로 하는 클래스를 인스턴스해야하며, 그때 클래스의 이름으로 해당 클래스를 인스턴스하기 위한 Class.forName() 코드를 사용한다.  
&nbsp;&nbsp;&nbsp;&nbsp;Oracle DB를 자바와 연동하기 위해서는 Oracle DB와 연결 드라이버를 알고 있어야 한다. 자바에서는 Oracle DB를 연동하기 위한 드라이버를 제공하고 있다. JDBC(Java DataBase Connectivity)라는 드라이버를 사용한다. JDBC는 자바 프로그램이 DB와 연결되어 데이터를 주고 받을 수 있게 해주는 프로그래밍 인터페이스이다. 즉, 자바 언어로 SQL로 작성된 쿼리 객체를 DB로 전달하여 데이터베이스 안의 테이블을 대상으로 작업을 할 수 있도록 해준다.  
&nbsp;&nbsp;&nbsp;&nbsp;JDBC의 드라이버에는 4 종류가 있다.

 - TYPE1. JDBC-ODBC 브리지 드라이버
 sun 사에서 기본적으로 제공하는 JDBC 드라이버로써 윈도우 운영체제에서 제공되는 ODBC를 사용할 수 있도록 해준다. sun.jdbc.odbc 패키지를 통해 해당 클래스들이 제공된다.
 - TYPE2. NATIVE 드라이버
 DBMS의 C언어나 C++언어용 API를 호출하는 자바언어로 작성된 메소드, Oracle의 OCI 드라이버 타입
 - TYPE3 JDBC 네트워크 연결 드라이버
 JDBC 메소드 호출을 DBMS에 독립적인 네트워크 프로토콜로 바꾸어 미들웨어에 전달하고, 이 미들웨어가 DBMS와 통신을 하는 형태의 순수 자바로 작성된 드라이버
 - TYPE4 DBMS 프로토콜 준수 드라이버
 가장 일반적으로 사용되는 드라이버로 DBMS를 직접 접속하여 사용, DBMS 프로토콜을 직접 사용하므로 DBMS에 종속되어 DBMS마다 별도 만들어진다.
 
 &nbsp;&nbsp;&nbsp;&nbsp;이 중에서 가장 많이 사용되는 것은 TYPE4, Oracle thin 드라이버이다.  Oracle thin 드라이버의 드라이버 클래스와 연결 URL은 다음과 같다.

 - 드라이버 클래스 : oracle.jdbc.driver.OracleDriver
- “jdbc:oracle:thin:@<dbname>:1521:ORA”
	
&nbsp;&nbsp;&nbsp;&nbsp;Oracle의 JDBC 드라이버는 환경변수로 설정해서 잡아놓는다. 이를 위해서 환경변수로 classpath를 설정한다. Oracle  홈페이지의 Oracle 11g 압축파일 2개를 다운받은 다음에, 동일 파일에 압축을 해체한다. 분할된 파일이라 압축을 해제하면서 동시에 병합될 것이다. 폴더 2개를 병합한 후에 setup.exe를 실행하면 Oracle 11g를 설치할 수 있다.  
&nbsp;&nbsp;&nbsp;&nbsp;Oracle 11g는 드라이버\app\에 자동으로 설치된다. c\app\username\product\11.2.0\dbhome_1\jdbc\lib 경로를 이동하면 ojdbc 압축파일들이 있다. 다양한 드라이버를 모아놓은 것 같은데, 이 중 ojdbc6.jar를 classpath로 잡아주면 된다. 관습적으로 ojdbc6.jar를 jdk가 설치된 폴더 하위에 classes 폴더를 별도로 만들어놓고 옮겨놓는다. 그 후에 classpath를 잡아준다. classpath의 경로는 다음과 같이 잡는다.  
&nbsp;&nbsp;&nbsp;&nbsp;classpath : " .;%JAVA_HOME%\classes\ojdbc6.jar"
&nbsp;&nbsp;&nbsp;&nbsp;이렇게 하면 ODBC 드라이버의 클래스 파일을 통해서 자바와 Oracle DB를 연결할 수 있는 것이다.  
&nbsp;&nbsp;&nbsp;&nbsp;위에서 말한 것처럼,  jdbc의 드라이브 클래스를 찾아 인스턴스한다. 그때 사용하는 클래스가 Class.forName() 이다. 
	
	Class.forName("oracle.jdbc.driver.OracleDriver");
	Connection conn = DriverManager.getConnection(
        "jdbc:oracle:thin:@localhost:1521:"SID"/*전역데이터베이스이름*/
													,"scott"
													,"tiger");
	
	/*
    jdbc:oracle:thin: - Oracle jdbc thin 드라이버 찾는 문자열  
    @localhost - @ 구분자, localhost : 데이터 베이스가 설치된 컴퓨터 이름, 4가지가 있다.  
                    ip 주소/컴퓨터 이름/127.0.0.1/localhost  
    :1521 : 컴퓨터에서 설치된 어플리케이션, Oracle 데이터베이스의 port 번호  
    :orclKJW000 : Oracle 데이터베이스의 내부에 있는 식별자 SID : 전역데이터베이스 이름
    */
		
	Statement stmt = con.createStatement();
	ResultSet rs = stmt.executeQuery("SELECT EMPNO AS EE FROM EMP");
	while (rs.next())
	System.out.print(rs.getString(1)+ " : ");
	System.out.println("");

&nbsp;&nbsp;&nbsp;&nbsp;위의 코드를 통해서 자바와 Oracle DB를 접속하고 Oracle DB의 테이블을 가져와서 콘솔에 출력할 수 있다. 여기서 코드에 의문이 든다. Class.forName()은 단순히 클래스의 함수를 호출하기만 하는 것 같은데, 설명에는 자바 프로그램과 Oracle DB를 연결한다고 한다. 내부적인 코드가 어떤 방법으로 돌고 있을까 싶어서 한 번 찾아보았다.  
	
	 @CallerSensitive
    public static Class<?> forName(String className)
                throws ClassNotFoundException {
        Class<?> caller = Reflection.getCallerClass();
        return forName0(className, true, ClassLoader.getClassLoader(caller), caller);
    }
	
&nbsp;&nbsp;&nbsp;&nbsp;Reflection 이라는 개념이 있는데, 이거는 나중에 알아보기로 하자. 메모만 해둬야겠다.
코드를 전부 분석할 지식은 없다. 하지만 return으로 forName0를 호출한다. 지난번도 그렇고 지금도 그런데 아마 자바에서는 함수명에 0가 첨가되는 경우에는 native 키워드가 있는 함수인것 같다. forname0()도 native 함수여서 java API로는 확인할 수가 없다. C나 C++ 로 만든 외부 코드를 자바에서 사용하기 위한 기술인 것 같은데, 이것도 메모해놓고 나중에 코드를 찾아보도록 해야겠다.  
&nbsp;&nbsp;&nbsp;&nbsp;다만, forname의 API 주석에 forName(String className)과 forname(String className, boolean initialize, ClassLoader loader)이 동등한 기능을 하며, 기본값으로 boolean 이 true 값이며, true값이면 초기화를 한다고 한다. 초기화해서 변수 및 함수들이 메모리에 static으로 올라갔기 때문에 참조변수를 사용하거나 하지 않아도 자원들을 사용할 수 있는 것 같다.  
&nbsp;&nbsp;&nbsp;&nbsp;그러나 이것도 버전마다 달라서 ojdbc 4 이후에 나온 버전으로는 Class.forName()을 하지 않더라도 자동으로 메모리에 올려주는 것 같다. 주석처리를 해도 정상적으로 아래 코드가 작동한다.  

    Connection conn = DriverManager.getConnection(URL, ID, PW)
    >> 연결한 Oracle DB에서 특정 계정 컴퓨터의 특정 계정을 연결하게끔 해주는 코드이다.  

    Statement stmt = con.createStatement()
    >> conn 참조변수의 createStatement()로 SQL 문장을 담을 SQL 객체를 생성한다.

    ResultSet rs = stmt.executeQuery("SELECT EMPNO AS EE FROM EMP")
    >> 생성된 stmt 객체(SQL 객체)에 SQL 문을 담아서 extcuteQuery로 보낸다.
       참조변수 rs는 그 Oracle DB에서 반환된 결과값을 가리킨다.

    while (rs.next()){
        System.out.println("rs.getString(Column index or Column label")
    }
    >> 결과값 rs에 .next() 함수로 데이터를 row 별로 돌면서 콘솔에 호출한다.
    저 .next()에는 cursor 개념이 존재해서 cursor로 출력하는 것 같다.  

&nbsp;&nbsp;&nbsp;&nbsp;Statement 와 비슷한 기능을 하는 prepareStatement가 있다. prepareStatement는 먼저 SQL 문을 기재한다. 따라서 executeQuery() 에 매개변수가 없다.

pstmt = conn.prepareStatement(OracleTest_1.EMP_SELECT "SQL문" );
rsRs = pstmt.executeQuery();

&nbsp;&nbsp;&nbsp;&nbsp;API에는 동일한 SQL문을 많이 실행할 경우에는 prepareStatement가 유리하다고 한다. statement는 쿼리 문장 분석> 컴파일 > 실행을 반복적으로 해주어야 하지만, prepareStatement는 처음 1회 동작 실행 후, 캐시에 담아서 재사용 한다고 한다. 정확한 내용은 조금 더 공부를 한 다음에 첨가할 수 있도록 해야겠다.  
기본적인 연결과 동작은 이렇다. 일반적으로는 VO 클래스로 get, set을 할 수 있도록 코드를 하고, VO클래스의 참조변수에 SQL문을 조회된 데이터들을 담아서 이동한다.
	
	while (rs.next()){
	EmpVO evo = new EmpVO(); //get, set 함수로 조회된 데이터를 담고, 출력할 수 있다.
	evo.setEmpno(rsRS.getString("EMPNO");
	// set..

	.. //
	ArrayList<EmpVO> aLIst = new ArrayList<EmpVO)();
	aList.add(evo) // ArrayList 에 해당 데이터를 담다.
		System.out.println("rs.getString(Column index or Column label")
	}

&nbsp;&nbsp;&nbsp;&nbsp;이후에 배열의 길이로 순환하면서 get 함수를 사용하면 데이터를 출력할 수 있다.
