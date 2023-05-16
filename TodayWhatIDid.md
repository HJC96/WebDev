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

-웹 페이지 
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
