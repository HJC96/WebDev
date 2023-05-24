### 스프링 Web MVC 특징

#### 스프링 MVC 전체 흐름
<img width="534" alt="image" src="https://github.com/HJC96/WebDev/assets/87226129/2d5b8847-5677-4151-b5fe-5e834a120e9d">

(https://developer.ucsd.edu/develop/user-interface-3/applying-mvc.html)

#### 기존 구조와 다른점
1. Front-Controller 패턴을 이용
  - 스프링 MVC에서 모든 요청이 반드시 DispatcherServlet이라는 존재를 통해 실행
2. 어노테이션을 통한 코드 간결화
3. HttpServletRequest/HttpServletResponse를 이용하지 않아도 될만큼 추상화

#### 스피링 MVC에서 주로 사용하는 어노테이션
- 컨트롤러 선언부에서 사용하는 어노테이션
  - @Controller: 스프링 빈의 처리됨을 명시
  - @RestController: REST 방식의 처리를 위한 컨트롤러임을 명시
  - @RequestMapping: 특정한 URL 패턴에 맞는 컨트롤러인지를 명시
- 메소드 선언부에서 사용하는 어노테이션
  - @<Get, Post, Delete, Put>Mapping: HTTP 전송 방식에 따라 해당 메소드를 지정하는 경우에 사용
  - @RequestMapping: GET/POST 방식 모두를 지원하는 경우에 사용
  - @ResponseBody: REST 방식에서 사용
- 메소드의 파라미터에 사용하는 어노테이션
  - @RequestParam: Requset에 있는 특정한 이름의 데이터를 파라미터로 받아서 처리하는 경우에 사용
  - @PathVariable: URL 경로의 일부를 변수로 삼아서 처리하기 위해서 사용
  - @ModelAttribute: 해당 파라미터는 반드시 Model에 포함되어서 다시 뷰(view)로 전달됨을 명시(주로 기본 자료형이나 Wrapper클래스, 문자열에 사용)
  - 기타 @SessionAttribute, @Valid, @RequestBody


