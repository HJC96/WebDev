# Today What I DID

2023-05-17
- 개발환경 세팅
  - IDE: IntellJ Ultimate
  - Application Server: Tomcat 
  - Jakarta EE

- 웹 페이지
  - 서블릿 코드 작성
~~~java
@WebServlet(name = "myServlet", urlPatterns = "/my") // 브라우저의 경로와 해당 서블릿을 연결하는 설정
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, ImagingOpException, IOException {

        resp.setContentType("text/html");

        PrintWriter out = resp.getWriter(); // PrintWriter 객체를 이용해서, 브라우저로 출력
        out.println("<html><body>");
        out.println("<h1>MyServlet<h1>");
        out.println("</body></html>");
    }
}
~~~
![image](https://github.com/HJC96/WebDev/assets/87226129/e146b5dd-b553-411b-8fa7-e5d9a19842d0)

- 웹 페이지 
  - JSP 코드 작성
~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>Test JSP PAGE</h1>
</body>
</html>
~~~

![image](https://github.com/HJC96/WebDev/assets/87226129/904311c4-efc9-4223-87bb-3c3d79812d9a)


- 웹 기본
  - 브라우저 정보 전달 방식
    - GET 방식
      - 주소창에 직접 원하는 데이터를 적거나 링크를 클릭 
    - 용도
      - 정보 조회  
    - POST 방식
      - POST 방식
        - 입력 화면에서 필요한 내용을 작성 후 '전송'과 같은 버튼 등을 클릭해서 호출
    - 용도
      - 회원 가입, 로그인 
  - 웹 서버 <-> 웹 어플리케이션 서버
    - 웹 서버는 정적 데이터(html,css, 이미지 파일)등의 데이터를 보내는 서버
    - 웹 어플리케이션 서버는 동적 데이터를 만들어 보낸다.     
  - 비 연결성
    - 우리가 '새로 고침'을 하는 이유는 브라우저와 서버의 연결이 끝났기 때문이다.
    - 서버에서는 하나의 요청을 빨리 처리하고 연결을 종료해서 다음 요청을 받을 수 있다면, 적은 리소스를 이용해 많은 수의 요청을 처리할 수 있다.

- 자바 서버 사이드 프로그래밍
  - JAVA EE 기술 스펙
    - 서버 쪽에서 동시에 여러 요청이 들어온다면 어떻게 처리해야 하는가?, 서버에서 문제가 생기면 이를 어떻게 처리해야 하는가? 등의 기술을 스펙으로 정리해둔것
  - 서블릿 기술 vs JSP 기술
    - 서블릿
      - **API**들을 정의해 서버에서 동적으로 요청과 응답을 처리  
    - JSP(Java Server Pages)  
      - 서블릿 기술과 동일하게 서버에서 동적으로 데이터를 구성하지만 목적이 다르다.
      - JSP는 HTML 코드를 그대로 이용하고 필요할때 약간의 자바코드를 넣는반면, 서블릿 코드는 자바 코드를 이용해 HTML 문자열을 만들어 낸다.
      - 따라서 JSP가 화면 구성에 편리하다.

- JSP를 이용해서 GET/POST 처리
  - GET 방식
~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<form>
  <input type="number" name ="num1">
  <input type="number" name ="num2">
  <button type="submit">SEND</button>
</form>

</body>
</html>
~~~

![image](https://github.com/HJC96/WebDev/assets/87226129/c3c2cf01-ca0e-4d58-880b-e77e8cbb7e9c)

- JSP를 이용해서 GET/POST 처리
  - POST 방식
~~~jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--전송방식 post로 변경, action을 calcResult.jsp로 전송--%>
<form action="calcResult.jsp" method = "post">
  <input type="number" name ="num1">
  <input type="number" name ="num2">
  <button type="submit">SEND</button>
</form>

</body>
</html>
~~~


~~~jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
  <h1>NUM1 ${param.num1}</h1>
  <h1>NUM2 ${param.num2}</h1>

  <h1>SUM ${Integer.parseInt(param.num1) + Integer.parseInt(param.num2)}</h1>
</body>
</html>
~~~
![image](https://github.com/HJC96/WebDev/assets/87226129/68b515c0-d8fb-4dc8-920b-aeefffe13df2)


       
