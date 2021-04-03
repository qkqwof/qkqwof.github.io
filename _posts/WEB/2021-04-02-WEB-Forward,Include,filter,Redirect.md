---
layout: post
current: post
cover:  assets/built/images/jdbc.jfif
navigation: True
title: web(6) - Forward,Include,filter,Redirect #
date: 2021-04-02 16:40:00 #
tags: [WEB] #태그명
class: post-template
subclass: 'post tag-WEB'
author: qkqwof #author.yml에서 설정한 author
---

## RequestDispatcher가 가지고 있는 forward(req,res) 방식, include(req,res) 방식의 페이지 이동 방법 + Filter

1. forward 방식

forward 하려는 페이지로 현재 서블릿의 모든 권한(req,res)를 가지고 서버 내에 있는 다른 페이지로 직접 이동
제어권이 완전히 이동하는 페이지로 가버리기 때문에 다시 이전 서블릿으로 되돌아 오지 않는다.
그렇기 때문에 서블릿에서 응답 관련된 객체 PrintWriter를 사용할 일이 없다. 철저하게 로직 중심으로 작성

**결과페이지의 url 주소를 보면, 결과페이지인 jsp 주소가 아니라 이전 서블릿 주소를 출력되어 진다. 모든 권한(req,res)을 가지고 이동했기 때문에 서블릿 이름으로 주소가 명시되어 진다.**

2. include

만약에 서블릿의 PrintWriter 객체를 이용하려 한다면 include 방식으로 페이지를 이동할 수 있다.
include에 담겨져 있는 모든 내용을 가지고 서블릿에 포함되므로 including을 한 다음에는 다시 서블릿 이후 과정을 마무리 짓고 서블릿이 직접 응답할 수 있다.

하지만 이렇게 사용되는 부분은 거의 없고 위 1번처럼 주로 foward 방식을 훨씬 더 많이 사용한다.

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2 align="center"> 단순검색 </h2><p>
<form action="SMS">
 조회 ID <input type="text" name="id" required="required">
 <input type="submit" value="단순조회">
</form>
</body>
</html>
```

``` java
package servlet.controller;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import servlet.model.MemberVo;


@WebServlet("/SMS")
public class SearchMemberServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doProcess(request, response);
	}

	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		doProcess(request, response);
	}

	
	protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

		PrintWriter out = response.getWriter();
		//로직은 여기다 작성
		/*
		 * 1. 폼값 받아서
		 * 2. dao 리턴 받고
		 * 3. 비즈니스 로직 호출한 결과로 vo객체 리턴 받고 
		 * 4. 리턴 받은 값을 Attribute에 바인딩 -- 어떤 Attribute를 사용할건지 잘 생각한다.
		 * 5. 네비게이션 --- 서버 내에 있는 jsp로 바로 이동
		 */
		
		String id = request.getParameter("id");
		String password = request.getParameter("password");
		String name = request.getParameter("name");
		String address = request.getParameter("address");
		
		//DAO 비즈니스 로직 호출했다 치고...
		
		request.setAttribute("id", id);//request에 바인딩...
		
		// 서버 내에 있는 jsp로 바로 이동 
		request.getRequestDispatcher("result_view.jsp").forward(request, response);
  		out.println("<h2>이 부분 보일까요?</h2>");
	}
}
```

폼값을 받아서 attribute에 바인딩하였다. 그 후에 getRequestDispatcher로 직접 이동이 가능하고 forward를 사용하여 이동한다. 이럴 때 처음 html 파일에서 request와 response를 그대로 가져간다. 그리고 마지막에 "이 부분 보일까요" 부분은 이미 forward가 호출되어서 다른 페이지로 이동되었기 때문에 실행되지 않는다. 

![forward](https://user-images.githubusercontent.com/76687078/113407678-c31bb200-93e8-11eb-8783-94d700bb50ae.png)

주소가 result_view.jsp인데 처음 html의 요청으로 페이지만 이동하여 req, res를 모두 가져와서 사용하였다.


![필터 전](https://user-images.githubusercontent.com/76687078/113407946-581eab00-93e9-11eb-8e2c-c12061cbdeb2.png)

include를 사용해보았다.
include 방식으로 페이지를 이동할 때 
PrintWriter 객체를 이용할 수 있다. 

분명 PrintWriter 객체로 

``` java
out.println("<h2>이 부분 보일까요?</h2>");
```

를 호출하였는데 한글이 아닌 ?만 나오는 것을 확인할 수 있다. 원래는 servlet에서 한글을 인코딩하는 작업을 하였는데 그때마다 하기에는 귀찮을 경우가 많다. 이럴 떄 사용하는 것이 filter이다. 

![필터](https://user-images.githubusercontent.com/76687078/113408454-5a353980-93ea-11eb-9401-e7928d21544c.jpg)

그림과 같이 클라이언트가 요청하는 것을 가로채서 인코딩 작업을 해버린다.

``` java
import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.annotation.WebInitParam;

@WebFilter(
			value = {"/*"},//모든 요청을 필터링 하겠다!!!!
			initParams = @WebInitParam(name="encoding", value="utf-8")
		)
public class EncodingFilter implements Filter {

	public void destroy() {
		// TODO Auto-generated method stub
	}

	
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		//양방향 한글처리
		request.setCharacterEncoding("utf-8");
		response.setContentType("text/html; charset=utf-8");
		
		//서버상의 다음 컴포넌트에게 계속 필터링한 작업이 연결되도록 하기 위한 작업...반드시 있어야 한다.
		chain.doFilter(request, response);
	}

	
	public void init(FilterConfig fConfig) throws ServletException {
		// TODO Auto-generated method stub
	}

}
```

Anotation부분에서 모든 요청과 응답을 가로챈다. 그 후에 doFilter()를 호출해서 한글 인코딩 작업을 해준다.

![필터 후](https://user-images.githubusercontent.com/76687078/113408857-1858c300-93eb-11eb-9aa1-259699304220.png)

적용하면 한글부분이 보이는 것을 알 수 있다.

## Redirect

![forward,redirect](https://user-images.githubusercontent.com/76687078/113409320-fd3a8300-93eb-11eb-9fb2-67f0b1ba98e2.jpg)

그림과 같이 **forward**는 요청을 받았을 때, 서블릿에서 다른 페이지로 그대로 넘겨주어서 WAS 밖으로 이동하지 않는다.  **Redirect**는 요청을 받고 서블릿에서 응답을 보낸 후 브라우저에 다시 돌아갔다가 헤더 방향을 바꿔서 다른 서버나 헤더로 이동한다. 보통 정적 페이지로 이동할 떄 많이 사용하고 에러페이지를 핸들링할 때 많이 사용한다.

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2>검색할 단어를 영문으로 입력하세요.</h2>
<form action="reDirect1.jsp">
Search Word :: <input type = "text" name="word">
<input type = "submit" value="Search">
</form>
</body>
</html>
```

``` html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<%
	String url = "https://search.naver.com/search.naver?";

	String keyword = request.getParameter("word");
	
	url += "&query=" + keyword;
	
	response.sendRedirect(url);
%>
</body>
</html>
```

sendredirect가 브라우저로부터 온 요청에 응답을 한 후 다시 요청하는 것이기 때문에 response 객체에 담는다.

![검색](https://user-images.githubusercontent.com/76687078/113410017-653d9900-93ed-11eb-997f-df8b449c9081.png)

![jsp](https://user-images.githubusercontent.com/76687078/113410059-830afe00-93ed-11eb-817d-1dbd250a3274.png)

정보 입력 후 검색을 누르면 jsp에서 설정한 페이지로 재요청이 들어가서 네이버 페이지가 연결되었다.

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h2 align="center">Rediect 방식의 페이지 이동...Error Page Handling..</h2>
<form action="Rediect2">
Choose :: <input type="checkbox" name="choose" value="Busan">
<input type="submit" value="Value send">
</form>
</body>
</html>
```

``` java
package servlet.controller;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


@WebServlet("/Rediect2")
public class RedirectServlet2 extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		/*
		 * checkbox를 선택하지 않으면...error page로 이동...-> Redirect 방법 사용
		 * checkbox를 선택했다면 ....  결과 페이지로 이동 ---> Forward 방법 이용
		 */
		
		String choose = request.getParameter("choose");
		if(choose == null) {
			response.sendRedirect("./error/error.html");
		}else {
			request.getRequestDispatcher("redirect2.jsp").forward(request, response);
		}
			
	}

}
```

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<strong>
현재 홈페이지 보수작업 중입니다....<br>
더 좋은 모습으로 5월에 만나요...<br>
관리자에게 궁금한 사안은 메일로 보내시기 바랍니다.<br>
qkqwof@naver.com<br>
<img src= "../img/gs.jpg" width="10%" height="15%">
</strong>
</body>
</html>
```

``` html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h3><%= request.getParameter("choose") %> 정보를 출력합니다..</h3>
</body>
</html>
```

![페이지 이동](https://user-images.githubusercontent.com/76687078/113410351-2fe57b00-93ee-11eb-8e14-842e51ad6e5a.png)

![정보확인](https://user-images.githubusercontent.com/76687078/113410326-1d6b4180-93ee-11eb-96d1-21ee5cbb562c.png)

![에러](https://user-images.githubusercontent.com/76687078/113410396-455aa500-93ee-11eb-9f9b-085fb4522dc5.png)

첫번째 그림에서 체크를 하게 되면 정보를 출력하는 페이지로 이동하고 체크하지 않으면 에러 메시지를 나타내는 페이지로 이동하게 만들었다.