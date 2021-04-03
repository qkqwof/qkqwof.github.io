---
layout: post
current: post
cover:  assets/built/images/jdbc.jfif
navigation: True
title: WEB(4) - Life_Cycle,ServletConfig #
date: 2021-03-31 16:40:00 #
tags: [WEB] #태그명
class: post-template
subclass: 'post tag-WEB'
author: qkqwof #author.yml에서 설정한 author
---

## Servlet의 Life Cycle

1. DD파일을 읽어들인다. 
 
**DD(Deployment Descriptor : 배포서술자)는 WEB-INF폴더 아래 web.xml이라는 파일명**

2. Servlet Instance가 생성된다. 여기서 기본 생성자가 호출된다.

3. init()메서드를 호출하여 초기화에 필요한 작업을 실행한다.


**이렇게 3번까지의 작업이 Ready-on상태이다.(단 한번만 진행)**

4. 요청이 들어오면 service()가 먼저 호출되고 doGet() 혹은 doPost() 중 하나가 재호출된다.

**동시에  Request, Response객체가 생성되고 passing into 된다.**

5. 클라이언트의 요청을 처리하고 응답을 내보낸다.

6. 쓰레드(service()), Request, Response객체가 소멸하고 메모리에서 unbound된다.

**서블릿 객체가 소멸할 때는 Servlet클래스의 destroy()메서드가 호출되어야 하는데 이는 서버가 stop되면 실행된다.
즉 서버 구동이 멈출 때 Servlet 객체가 소멸한다.**

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2 align="center">Life cycle...CallBack Method....By Container</h2>
<a href="LIFE2">Life Cycle Test</a>
</body>
</html>
```

클라이언트의 요청을 처리한 결과의 자바 파일이다. 필드에 지정된 count값을 서블릿이 출력하지 않고
정보를 jsp에게 넘기고
결과페이지 처리를 jsp가 하도록 로직을 작성하였다. 그리고 페이지 연결은 a태그로 하였다.

출력을 해보면 콘솔에 Life Cycle의 순서대로 생성자 생성 이후에 init(), service() 순으로 출력되고 마지막에 연결을 끊으면 destroy()가 출력된다.
loadOnStartup라는 명령어로 원래는 2,3,4가 함께 출력이 되었는데 이후에는 따로 출력된다는 것을 알 수 있었다.

``` java
package servlet.life;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(urlPatterns = {"/LIFE2"}, loadOnStartup = 1)
public class LifeCycleServlet2 extends HttpServlet {
	private static final long serialVersionUID = 1L;
    private int count = 0; //필드...   
    
    public LifeCycleServlet2() {
        System.out.println("1.LifeCycleServlet1 생성자 호출...by Container...");
        
    }

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("3. service()-->doget() or doPost() 호출....by Container...");
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html; charset=utf-8");
		PrintWriter out = response.getWriter();
		count++;//
		
//      이 부분의 내용이 jsp에 들어가는 것이 역할 분담에 더 맞다.	
//		out.println("<html><body bgcolor='yellow'>");
//		out.println("<h3>LifeCycle Callback Method...</h3>");
//		out.println("<b>count :: " + ++count + "</b>");
//		out.println("</body></html>");
		
		out.println("<a href=life2.jsp?cnt="+count+">여기를 클릭하시면 JSP페이지로 이동합니다.</a>");//이떄 count값을 가지고 JSP로 가려면?
		
		out.close();
		
	}//
	
	@Override
	public void init() throws ServletException {
		  System.out.println("2. init() 호출...by Container...");
	}
	
	@Override
	public void destroy() {
		System.out.println("4. destroy() 호출...by Container...");
	}
}
```
이 jsp 파일을 생성하여 페이지가 sevlet으로부터 jsp파일로 이동되고, 서버와의 연결이 될 때마다 카운트가 늘어난다. 

``` jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body bgcolor='cyan'>
<h3>JSP Result Page</h3>
<b>Count Value :: </b>
<%= request.getParameter("cnt") %> 
</body>
</html>
```

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2 align="center">Life cycle...CallBack Method....By Container</h2>
<a href="LIFE3">Life Cycle Test</a>
</body>
</html>
```

``` java
@WebServlet(urlPatterns = {"/LIFE3"}, loadOnStartup = 1)
public class LifeCycleServlet3step extends HttpServlet {
	private static final long serialVersionUID = 1L;
    private int count = 0; //필드... 
    private String path = "C:\\encore_PJY\\util\\web_util\\life\\count.txt";
    
    
    public LifeCycleServlet3step() {
        System.out.println("1.LifeCycleServlet1 생성자 호출...by Container...");
        
    }

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("3. service()-->doget() or doPost() 호출....by Container...");
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html; charset=utf-8");
		
		PrintWriter out = response.getWriter();
		
		
		//이 부분의 내용이 jsp에 들어가는 것이 역할 분담에 더 맞다.	
				out.println("<html><body bgcolor='yellow'>");
				out.println("<h3>LifeCycle Callback Method...</h3>");
				out.println("<b>count :: " + ++count + "</b>");
				out.println("</body></html>");
				
				out.close();
	}//
	
	@Override
	public void init() throws ServletException {
		  System.out.println("2. init() 호출...by Container...");
		  //파일에 저장된 내용을 읽어들이는 로직은 Initialization 부분인 init에서 해준다.
		  try {
			  BufferedReader br = new BufferedReader(new FileReader(path));
			  String str = br.readLine();
			  //읽어들인 count값을 필드에 다시 할당함!!
			  count = Integer.parseInt(str);
			  System.out.println("count.txt의 값을 읽어서 필드 count에 다시 할당!!업데이트 성공!!");
		  }catch(Exception e) {
			  System.out.println("파일을 읽어들이는데 문제가 발생했습니다...fail...");
		  }
	}
	
	@Override
	public void destroy() {
		System.out.println("4. destroy() 호출...by Container...");
		//인스턴스가 메모리에서 제거되기 전에 필드값을 영구적으로 보관...파일에 저장
		//출력용 스트림을 생성..이곳에 필드값을 출력
		
		File f = new File(path);
		//디렉토리가 현재 존재하지 않는다면..디렉토리를 먼저 만들어 놓아야 그곳에 count.txt 파일이 생성
		f.getParentFile().mkdirs();
		try {
			PrintWriter pw = new PrintWriter(new FileWriter(f));
			pw.println(count);
			pw.close();
			System.out.println(path+" Count값 :: "+count+" 파일에 값 영구적으로 생성");
		}catch(Exception e) {
			System.out.println("출력용 스트림 생성 실패");
		}
	}
}
```

외부의 field의 값을 영구적으로 저장하기 위해서는 디비에 연결하거나 File을 저장하는 방법을 사용한다.

해당 예제에서는 count된 값을 지정된 경로로 해서 txt파일로 저장해보는 예제였다...
결과적으로 지정한 경로에 파일이 저장되었다.

![count](https://user-images.githubusercontent.com/76687078/113162760-85008000-927a-11eb-9316-b836ab8d276a.png)

## ServletConfig

- WAS와 연결된 객체이고 컨테이너 쪽에서 설정한 정보를 받아와서 서블릿을 초기화 시키는 객체이다.
이것은 init()에서 수행된다. 

- 애초에 SE 스펙에서와는 다르게 EE 스펙에서는 사람이 객체를 생성하는 것이 아닌 Servlet이 객체를 생성해주기 때문에 그 전까지 해왔던 것처럼 기본 생성자를 오버라이딩하여 초기화 작업을 할 수가 없다. 따라서 이 작업은 EE스펙에서 굉장히 유효하다.

- service()와 시점이 다르고 객체를 가져올 때 getInitParameter를 쓴다.

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<marquee bgcolor="orange"><h1>Servletconfig를 이용한 A Servlet Initialization....during init()</h1></marquee>
<a href="SC1">Here Click</a>
</body>
</html>
```

``` java
public class ServletConfigTest1 extends HttpServlet {
	private static final long serialVersionUID = 1L;
	//필드 추가...
	private String name, address;
	
    public ServletConfigTest1() {
        System.out.println("생성자 호출...인스턴스 이때 생성 됩니다..");
    }	
    @Override
    public void init() throws ServletException {
    	System.out.println("init() call...");
    	//서블릿의 필드 초기화...이 정보는 절대로 화면으로부터 받아온 정보가 아니다.
    	//그 이전에 훨씬 이전에 컨테이너에 keeping된 값을 끌어당겨왔다.
    	name=getInitParameter("custname");
    	address=getInitParameter("address");
    	System.out.println(name+","+address);    	
    }
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}
	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
		
		PrintWriter out = response.getWriter();		
		out.println("<html><body bgcolor=yellow>");	 
		out.println("<h2>A Servlet Configuration....Using ServletConfig</h2>");
		out.println("Your Name "+name+"<br>");
		out.println("Your Address "+address+"<br>");
		
		out.println("</body></html>");
		out.close();
	}
}
```

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">

  <servlet>
    <servlet-name>ServletConfigTest1</servlet-name>
    <servlet-class>servlet.config.ServletConfigTest1</servlet-class>
    
    <init-param>
    	<param-name>custname</param-name>
    	<param-value>James</param-value>
    </init-param>
    
    <init-param>
    	<param-name>address</param-name>
    	<param-value>NY</param-value>
    </init-param>
    
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
```

이 예제에서는 Anotation 대신 xml에서 서블릿 객체를 생성했다. 

![xml](https://user-images.githubusercontent.com/76687078/113165814-2ee10c00-927d-11eb-810e-b5d2c2a55053.png)

결과를 봤을 때 init-param을 통해 보낸 값들이 잘 나오는 것을 볼 수 있다.

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2 align="center">Using ServletConfig....Getting VIP Keys...</h2>
<form action="SC2" method="post">
VIP Cust Name <input type="text" name="cust" placeholder="고객 실제 이름을 적어주세요">
<input type="submit" value="SEND">
</form>
</body>
</html>
```

``` java
package servlet.config;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class ServletConfigTest2step extends HttpServlet {
	private static final long serialVersionUID = 1L;
	//필드 추가...
	private String path;
	private String custid;
    public ServletConfigTest2step() {
        System.out.println("생성자 호출...인스턴스 이때 생성 됩니다..");
    }	
    @Override
    public void init() throws ServletException {
    	System.out.println("init() call...");
    	//저장된 파일의 값을 읽어서 필드에 할당....로직을 작성    
    	path=getInitParameter("path");
    	
    	System.out.println("init()....configTest2...");    	
    	try {
    		BufferedReader br = new BufferedReader(new FileReader(path));
    		custid = br.readLine(); //수정
    		System.out.println("고객정보...credit no..."+custid);
    		
    		br.close();
    	}catch(Exception e) {
    		
    	}
    }
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}
	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
		
		PrintWriter out = response.getWriter();	
		
		//폼으로부터 받아온 값 읽어온다...getParameter();..getInitParameter()와 시점이 완전히 다르고 값을 받아오는 통로도 다르다...!!!
		String custname = request.getParameter("cust");
		
		out.println("<html><body bgcolor=yellow>");	 
		out.println("<h2>A Servlet Configuration....Using ServletConfig</h2>");
		
		out.println("<h3>"+custname+" , "+custid+" 로 인증되었습니다...서비스 진행가능 합니다...");
		
		out.println("</body></html>");
		out.close();
	}
}
```

path에 해당하는 text파일이 저장되어 있고 필드 custid에 경로가 path인 파일을 읽어 저장했다. 요청으로부터 파라미터를 받아 custName이라는 변수에 저장하였다.

![html페이지](https://user-images.githubusercontent.com/76687078/113166711-fee63880-927d-11eb-8e72-883cb0807957.png)

고객이름을 적고 send버튼을 누르면 text파일에 저장되어 있던 문자가 매칭되어 화면에 나타난다.


