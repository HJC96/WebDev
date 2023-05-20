## Web MVC

- web MVC 방식
  - 배경
    - 서블릿의 경우 자바 코드를 그대로 이용할 수 있지만 HTTP로 전달된 메시지를 HTML로 처리할때 많은 코드가 필요
    - JSP의 경우 HTML 코드를 재사용해 HTTP 작성에는 적합하지만, 자바 코드 재사용의 문제
  - MVC(Model, Viewer, Controller)
    - 컨트롤러: 서블릿
    - 뷰어: JSP   
    - 모델: 필요한 데이터 제공

- InputController
~~~java
package org.zerock.web_1.calc;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet (name = "inputController", urlPatterns = "/calc/input")
public class InputController extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
        System.out.println("InputController doGet");

        RequestDispatcher dispatcher = req.getRequestDispatcher("/WEB-INF/calc/input.jsp"); // 서블릿에 전달된 요청을 다른쪽으로 전달 

        dispatcher.forward(req,resp);
    }
}

~~~
- CalcController
~~~java
package org.zerock.web_1.calc;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "calcController", urlPatterns = "/calc/makeResult")
public class CalcController  extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        String num1 = req.getParameter("num1");
        String num2 = req.getParameter("num2");

        System.out.printf("num1: %s", num1);
        System.out.printf("num2: %s", num2);
        
        resp.sendRedirect("/index"); // 처리가 끝난후 다른 경로로 이동하게 하는 것

    }
}
~~~




EL(Expression Language)
- 개념
  - JSP 출력용 언어
  - 출력만을 담당하기 때문에 제어문이나 반복문과 같이 '식(expression)'이 아닌 '문(statement)'을 처리하기 위해서 JSTL라이브러리가 필요하다.
- 용법
  - '${}'
- 예제
~~~jsp
<h3>${1 + 2 + 3}</h3>
<h3>${"AAA" += "BBB"}</h3>
<h3>${"AAA".equals("AAA")}</h3>

<h4>${list[0].title}</h4>
<h4>${list[0].getTitle()}</h4>
~~~
 
 
JSTL(JavaServer Pages Standard Tag Library)
- 개념
  - jsp에서 statement를 처리하기 위한 라이브러리
  - 자바 문법보다 간결하게 제어문, 반복문, 선언문 등을 처리가능
- 속성
  - var
    - EL에서 사용될 변수 이름
  - itmes
    - List, Set, Map, Enumeration, Iterator 등의 컬렉션 
  - begin/end
    - 반복의 시작/끝 값 

### 실전 투입

- 라이브러리 추가
~~~build.gradle
    implementation group: 'jstl', name: 'jstl', version: '1.2'
~~~
- JSP 상단에 태그 추가
~~~
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
~~~
- 실제 예제
~~~jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <title>List Page</title>
</head>
<body>
<h1>List Page</h1>

<ul>
    <c:forEach var="dto" items="${list}">
        <li>${dto}</li>
    </c:forEach>
</ul>

</body>
</html>
~~~
![image](https://github.com/HJC96/WebDev/assets/87226129/90ebdd2f-c67c-45b7-a9cd-02025870dfbd)


Todo조회

![image](https://github.com/HJC96/WebDev/assets/87226129/17fd5960-e935-4ad8-afb3-c400eecc6fc5)

