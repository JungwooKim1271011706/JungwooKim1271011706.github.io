---
title: 자바 - 20 (getProperty)
category:
- Java
tag:
- Java
---

System 클래스에 .getProperty( )라는 함수가 있다. 시스템 속성을 자바를 통해서 체크할 수 있는 것 같다. 

	String javaVersion = System.getProperty("java.version");
	System.out.println("javaVersion >>> : "+ javaVersion);
	String userDir = System.getProperty("user.dir");
	System.out.println("userDir 현재 디렉토리 >>> : "+ userDir);
	String osName = System.getProperty("os.name");
	System.out.println("osName 운영체제이름 >>> : "+ osName);
	String fileSeparator = System.getProperty("file.separator");
	System.out.println("fileSeparator 파일 구분문자 >>> : "+ fileSeparator);
	String pathSeparator = System.getProperty("path.separator");
	System.out.println("lineSeparator 행 구분문자 >>> : "+ pathSeparator);
	java.util.Properties p = System.getProperties();
	System.out.println(p);
	
아마 api 뒤져보면 더 나올것 같다. 나중에 코드를 분석해서 정리하는 시간을 가져봐야 겠다.
