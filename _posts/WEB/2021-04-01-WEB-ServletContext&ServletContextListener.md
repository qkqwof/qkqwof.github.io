---
layout: post
current: post
cover:  assets/built/images/jdbc.jfif
navigation: True
title: WEB(5) - ServletContext&ServletContextListener #
date: 2021-04-01 16:40:00 #
tags: [WEB] #태그명
class: post-template
subclass: 'post tag-WEB'
author: qkqwof #author.yml에서 설정한 author
---

## ServletContext

DD파일을 읽게 되면 제일 처음에 ServletContext가 생성된다. 이것은 ServletConfig와 정보를 받아오는 방식이 비슷한데 root는 컨테이너인 DD이다. 
하나의 정보를 서블릿 내에서 초기화 할 때는 ServletConfig를 이용했지만 여러개의 정보를 주고 받을 때는 ServletContext를 이용한다. ServletContext는 정보의 교환 및 값의 정보 전달을 위해서 setAttribute(), getAttribute()를 사용한다. 이렇게 정보를 전달하기 위해서는 바인딩을 해줘야 한다.

- setAttribute("이름",object) => map방식 사용

- getAttribute("이름") => object 타입으로 반환

![context](https://user-images.githubusercontent.com/76687078/113398534-b6439200-93d9-11eb-88c8-6fc2cf3fdcbd.jpg)

위에 그림과 같이 servlet과 servlet 사이에 바로 접근하는 것이 아니라 ServletContext에 writing을 먼저 하고 context가 정보를 받아들이면 그 후에 다른 서블릿이 정보를 리딩하는 구조로 되어 있다.

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2 align="center">Communication of the Servlets with it's container</h2>
<form action="Writing" method="post">
UserID <input type="text" name="userId" required="required"><br><br>
UserPASS <input type="password" name="userPass" required="required"><br><br>
UserName <input type="text" name="userName" required="required"><br><br>
<input type="submit" value="SEND">
</form>
</body>
</html>
```

``` java
import java.io.IOException;
import java.io.PrintWriter;
import java.util.HashMap;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/*
 * 1. 폼값 받는다. getParameter()
 * 2. 받은 값을 가지고 객체를 생성
 * 3. Servletcontext를 반환 받는다...ServletConfig의 getServletContext()
 * 	  Servletcontext에 바인딩...setAttribute
 * 4. 바인딩된 정보를 다음 서블릿이 가져다 쓸 수 있도록 페이지 연결
 */

@WebServlet("/Writing")
public class WriteContext extends HttpServlet {
	private static final long serialVersionUID = 1L;
    //필드 추가
	private HashMap<String,String> userList;
	private ServletContext cont;
	
    public WriteContext() {
       
    }

	@Override
		public void init() throws ServletException {
			System.out.println("init() call...");
			//2. userList에 담는다.(vo객체가 없어서 map에 담는다.)
			cont = getServletContext();//servletConfig가 상속해줬다..그냥 호출해서 사용하면 됌.
			System.out.println("Writing ServletContext...return..."+cont.getContextPath());
			System.out.println("Writing ServletContext...return..."+cont);
			userList =new HashMap<String,String>(); 
		}
	
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
		
		//1. 폼 값 받는다.
		String userId = request.getParameter("userId");
		String userPass = request.getParameter("userPass");
		String userName = request.getParameter("userName");
		
		//2. userList에 담는다.(vo객체가 없어서 map에 담는다.)
		userList.put("userId", userId);
		userList.put("userPass", userPass);
		userList.put("userName", userName);
		
		//바인딩
		cont.setAttribute("userList", userList);
		
		PrintWriter out = response.getWriter();		
		out.println("<html><body bgcolor='pink'>");
		out.println("<b>======Data Writing.... ServletContext======<br>");
		out.println("사용자의 정보가 등록되었습니다.....</b><br><br>");
		
		//다음 서블릿으로 연결
		out.println("<a href=Reading>접속자 명단 보기</a>");
		out.println("</body></html>");
		out.close();
	}
}
```

html에서 요구한 정보가 여러개이기 때문에 userList에 담고 context에 userList를 바인딩한다.

``` java
package servlet.context;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/*
 * 1. ServletContext 하나 받아온다
 * 2. getAttribute()를 사용해서 바인딩된 데이터를 가져온다.
 * 3. 가져온 정보를 웹 브라우저로 출력한다.
 * 
 */

@WebServlet("/Reading")
public class ReadContext extends HttpServlet {
	private static final long serialVersionUID = 1L;
    
	//필드 추가
		private HashMap<String,String> userList;
		private ServletContext cont;

	@Override
	public void init() throws ServletException {
		System.out.println("init() call...");
		cont = getServletContext();//servletConfig가 상속해줬다..그냥 호출해서 사용하면 됌.
		System.out.println("Reading ServletContext...return..."+cont.getContextPath());
		System.out.println("Reading ServletContext...return..."+cont);
		userList =new HashMap<String,String>(); 
			}
	
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request,response);
	}

	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
		
		PrintWriter out = response.getWriter();		
		out.println("<html><body>");
		
		userList = (HashMap)cont.getAttribute("userList");
		
		out.println("<h2>다음은 Attribute 안에 바인딩된 데이터를 찾아온 결과입니다.</h2><p>");
		Set<String> set=userList.keySet();
		Iterator<String> it = set.iterator();
		while(it.hasNext()) {
			String key = it.next();
			String value = userList.get(key);
			out.println(key + " : " + value + "<br>");
		}
		
		out.println("</body></html>");
		out.close();
	}
}
```

그 후에 정보를 다른 서블릿으로 전달하는 servlet을 추가하여 잘 전달한다.

![하바리](https://user-images.githubusercontent.com/76687078/113399466-3caca380-93db-11eb-8e4b-5da0f39d7ed7.png)

![next](https://user-images.githubusercontent.com/76687078/113399534-50f0a080-93db-11eb-8077-aef9c7a6ee51.png)

![final](https://user-images.githubusercontent.com/76687078/113399571-649c0700-93db-11eb-9660-a2c6b070432e.png)

**html -> writing 자바파일 -> reading 자바파일 순서대로 정보가 잘 출력되는 모습을 볼 수 있다.**

## ServletContextListener

![listener](https://user-images.githubusercontent.com/76687078/113400120-497dc700-93dc-11eb-9c9c-f091e8400053.jpg)

전체적인 모습을 보여준다.


``` html
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">

 	<context-param>
 		<param-name>path</param-name>
 		<param-value>WEB-INF\member.txt</param-value><!-- 상대경로 -->
 	</context-param>
  
</web-app>
```

ServletContextListener는 서블릿 차원이 아니라 global한 차원에서 진행하니까 xml파일로 작업해야 한다.

``` java
import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

/*
 * WAS에 이해서 호출되는 모든 라이프 사이클 메소드 정리...
 * 1. contextInitialized() <- 글로벌 지점
 * ================================= ready on
 * 2. 서블릿 생성자 호출
 * 3. init()
 * ===================================== repeat
 * 
 * 4. service() --> doGet() | doPost()
 * 5. destroy()
 * 6. contextDestroyed
 */


@WebListener
public class MyListener implements ServletContextListener {
	private ServletContext cont;
	
    public void contextDestroyed(ServletContextEvent sce)  { 
         System.out.println("contextInitialized....");
         
         //1. ServletContext 리턴 받음...이 때 sce로부터 받아야 한다.
         cont = sce.getServletContext();
         
         //2. DD파일의 경로를 읽어들인다.
         String filename = cont.getInitParameter("path");
         System.out.println("User File Path : " + filename);
         
         //3. getResourceAsStream()...
         InputStream is = null;
         BufferedReader br = null;
         try {
        	 System.out.println("getResourceAsStream()...");
        	 is = cont.getResourceAsStream(filename);
        	 
        	 System.out.println("BufferedReader 생성...");
        	 br = new BufferedReader(new InputStreamReader(is));
        	 
        	 String line = null;
        	 System.out.println("내용을 읽어들입니다...");
        	 while((line = br.readLine())!=null) {
  			   System.out.println(line);
  		   }
  	   }catch(Exception e) {
  		   System.out.println("파일을 읽어들이는데 실패했습니다...");
  	   }
  	  }

	
    public void contextInitialized(ServletContextEvent sce)  { 
    	System.out.println("contextDestroyed.....");
    }
	
}
```

생성자 호출 전에 이벤트가 발생하고 리스너의 contextinitialized가 호출된다.
servletcontextevent타입의 sce변수를 참조해 getServletContext를 호출하여 리턴값을 cont에 넣는다.
그 후 inputstream 타입의 is변수에 getresourceasstream()의 리턴값을 넣어주고 br객체 생성을 할 때 이용하고, br.readline으로 내용을 읽어들인다.

![실행](https://user-images.githubusercontent.com/76687078/113401074-c9586100-93dd-11eb-81b6-6547f9e58fa3.png)

프로젝트 서버를 실행하게 되면 그림과 같이 txt파일에 있는 내용들이 콘솔창에 나오는 것을 알 수 있다.