---
layout: post
current: post
cover:  assets/built/images/jdbc.jfif
navigation: True
title: WEB(3) - ServletAPI,Get_Post방식 #
date: 2021-03-30 16:40:00 #
tags: [WEB] #태그명
class: post-template
subclass: 'post tag-WEB'
author: qkqwof #author.yml에서 설정한 author
---

## Servlet 구조

![ServletAPI](https://user-images.githubusercontent.com/76687078/112987022-34ffbb80-919d-11eb-9e92-6754f514333f.jpg)

일단 서블릿의 상속 구조를 살펴보면 GenericServlet은 Servlet과 ServletConfig를 상속받고 있고 HttpServlet이 GenericServlet을 상속받고 있다. 
GenericServlet은 ServletRequest와 ServletResponse와 헤징 관계를 가지고 있다. ServletRequest의 getParameter, getParameterValue메서드를 이용할 수 있고, ServletResponse의 printWriter 메서드를 이용할 수 있다. 우리는 Http에 특화된 HttpServlet, HttpRequest, HttpResponse를 이용한다.

## GET방식과 POST 방식

![get방식과 post방식](https://user-images.githubusercontent.com/76687078/112987089-4b0d7c00-919d-11eb-83b6-1e720602b562.jpg)

우선 servlet에서 get이나 post방식을 적용하기 이전에 service() 메서드를 호출하고 이것이 재호출되는 방식으로 이루어져 있다.

- Get방식

1. 기본적으로 데이터 전달이 주목적이 아니다.
2. 주목적은 Page를 연결하는 것이다.

3. request객체에 Data가 담기지 않는다.

4. a태그로 링크를 줄 경우에
<a href="AServlet?id=abs&password=cv> 이런식으로 Query String 방식으로 전달한다.

5. 주소창에 값이 노출되어져서 서버에 전송하지 않는다.
-> 보안성 관련 문제 떄문에...

- Post방식

1. 전송할 데이터가 많을 떄 사용한다.(데이터 전송이 주목적!!)

2. request 객체의 body부분에 데이터가 담겨져서 전송된다. 따라서 데이터 노출이 잘되지 않는다.

3. 데이터 개수 및 크기 제한이 없다.

![전체적인 틀](https://user-images.githubusercontent.com/76687078/112987162-5f517900-919d-11eb-808e-37f581473855.jpg)

이 그림은 전체적인 개발 구조를 보여주는 그림이다.


**Get과 Post를 활용한 예제**

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
#wrap{
	text-align:center;
	width:450px;
	height:250px;
	border:1px solid black;
	margin:0 auto;
}


</style>
</head>
<body>
<div id="wrap">
<h2>Http Haeder Information....</h2>
<div align="left"><form action ="PHP" method="POST">
ID <input type="text" name="id"><br>
PASS <input type="text" name="password"><br>
<input type="submit" value="send"><br>
</form>
<hr style="border:1px solid gray;">
<a href="PHP?name=하바리&address=약수동">PrintHeaderServlet 으로 연결합니다.</a> 
</div>
</div>
</body>
</html>
```
첫화면을 보여주는 html파일

``` Java
package sevlet.header;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


@WebServlet("/PHP")
public class PrintHeaderServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
   
    public PrintHeaderServlet() {
        super();
        
    }

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}

	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}

	public void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html; charset=utf-8");
		PrintWriter out = response.getWriter();
		
	    String id = request.getParameter("id");
		String password = request.getParameter("password");
		String name = request.getParameter("name");
		String address = request.getParameter("address");
		out.println("<html><body>");
		out.println("<h2>=====Header Information Printed....=====</h2>");
		Enumeration headerNames = request.getHeaderNames();
	    while(headerNames.hasMoreElements()) {
	      String headerName = (String)headerNames.nextElement();
	      out.println("<p>" + headerName + ":" + request.getHeader(headerName));
	    }
	    out.println("<hr>");
	    out.println(id +","+password+","+name+","+address);
	    out.println("</BODY></HTML>");
	}
}
```

*send버튼이나 밑에 글씨를 누르면 다음 페이지를 넘어가고 header 정보를 보여주는 자바 파일*

![화면 캡처 2021-03-30 213856](https://user-images.githubusercontent.com/76687078/112990081-9a08e080-91a0-11eb-9a1d-7ad21f4efd33.png)

*html 결과페이지*

![post결과](https://user-images.githubusercontent.com/76687078/112990200-bf95ea00-91a0-11eb-87dd-2b9bf3488079.png)

*Post결과물 중 일부*

![get결과](https://user-images.githubusercontent.com/76687078/112990341-e7854d80-91a0-11eb-975b-74e06693c77c.png)

*get결과물 중 일부*