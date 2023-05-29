## JDBC 
- MaraiaDB 설치 및 개발환경 구성
  - db 생성
  - 사용자 계정 생성과 권한 추가
  - MariaDB와 IntelliJ 연동
  - 다음의 라이브러리 추가
~~~build.gradle
implementation 'org.mariadb.jdbc:mariadb-java-client:3.0.4'
~~~

- MariaDB - IntelliJ 연동 테스트 코드 작성(JDBC Driver)
~~~java
package org.zerock.dao;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.mariadb.jdbc.Driver;

import java.sql.Connection;
import java.sql.DriverManager;

public class ConncectTests {

    @Test
    public void testConnection() throws Exception{

        Class.forName("org.mariadb.jdbc.Driver"); // JDBC 드라이버 클래스를 메모리로 로딩

        Connection connection = DriverManager.getConnection( // DB내의 정보들을 통해 특정 DB에 연결
                "jdbc:mariadb://localhost:3306/webdb",
                "webuser",
                "webuser");

        Assertions.assertNotNull(connection);

        connection.close();  // DB연결 종료. JDBC는 DB와 연결을 잠깐씩 맺고 종료하는 형식으로 동작

    }
}

~~~

SQL
- DDL(Data Definition Language)
  - 테이블을 생성하거나 특정 객체 생성  
- DML(Data Manipulation Language)
  - insert/update/delete 등
  - selcet 쿼리와 차이
    - select 쿼리는 데이터를 반환하는 반면 DML은 몇개의 데이터가 처리되었는지 숫자로 결과 환환


### 데이터 insert
~~~sql
insert into tbl_todo(title, dueDate, finished)
values ('Test...','2023-05-19',1);
~~~

### 데이터 select
~~~sql
select * from tbl_todo where tno < 10;
~~~

### 데이터 update
~~~sql
update tbl_todo set finished = 0, title = 'Not Yet...' where tno = 3;
~~~

### 데이터 delete
~~~sql
delete from tbl_todo where tno > 5;
~~~

**용어정리**
- DAO
  - 데이터를 전문적으로 처리하는 객체

- VO(Value Object) 혹은 엔티티
  - 데이터베이스에서는 하나의 데이터를 엔티티라고 한다.
  - 자바 프로그램에서는 이를 처리하기 위해 테이블과 유사한 구조의 클래스를 만들어서 객체로 처리하는데,
  - 이때 데이터베이스의 엔티티를 자바 객체로 표현한 것이 VO이다.
 
 
- JDBC 구현
  - build.gradle에 다음 Lombok 라이브러리 추가 
  - HikariCP 라이브러리 추가
~~~build.gradle
    compileOnly 'org.projectlombok:lombok:1.18.24'
    annotationProcessor 'org.projectlombok:lombok:1.18.24'

    testCompileOnly 'org.projectlombok:lombok:1.18.24'
    testAnnotationProcessor 'org.projectlombok:lombok:1.18.24'

    implementation group: 'com.zaxxer', name: 'HikariCP', version: '5.0.0'
~~~


- getTime() 메소드 작성
  - try-catch 
~~~java
public class TodoDAO {
    public String getTime(){
        String now = null;
        try(Connection connection = ConnectionUtil.INSTANCE.getConnection();
            PreparedStatement preparedStatement = connection.prepareStatement("select now()");
            ResultSet resultSet = preparedStatement.executeQuery();
        ){
            resultSet.next();

            now = resultSet.getString(1);
        }catch (Exception e){
            e.printStackTrace();
        }
        return now;
    }
}
~~~
- getTime() 메소드 작성
  - @Cleanup 어노테이션
    - Lombok 라이브러리에 종속적이지만, try catch 문을 이용하여 가독성을 높일 수 있다.
~~~java
public class TodoDAO {
    public String getTime() throws Exception {

        @Cleanup Connection connection = ConnectionUtil.INSTANCE.getConnection();
        @Cleanup PreparedStatement preparedStatement = connection.prepareStatement("select now()");
        @Cleanup ResultSet resultSet = preparedStatement.executeQuery();

        resultSet.next();

        String  now = resultSet.getString(1);

        return now;
    }
}
~~~

- TodoDAO의 등록 기능 구현
~~~java
public void insert(TodoVO vo) throws Exception{
        String sql = "insert into tbl_todo (title, dueDate, finished) values (?, ?, ?)";

        @Cleanup Connection connection = ConnectionUtil.INSTANCE.getConnection();
        @Cleanup PreparedStatement preparedStatement = connection.prepareStatement(sql);

        preparedStatement.setString(1, vo.getTitle());
        preparedStatement.setDate(2, Date.valueOf(vo.getDueDate()));
        preparedStatement.setBoolean(3, vo.isFinished());

        preparedStatement.executeUpdate();
    }
~~~

**TestCode**
~~~java
    @Test
    public void testInsert() throws Exception{
        TodoVO todoVO = TodoVO.builder()
                .title("Sample Title...")
                .dueDate(LocalDate.of(2023,05,19))
                .build();

        todoDAO.insert(todoVO);
    }
~~~


- TodoDAO의 목록 기능 구현
~~~java

public List<TodoVO> selectAll()throws Exception{
        String sql = "select * from tbl_todo";
        @Cleanup Connection connection = ConnectionUtil.INSTANCE.getConnection();
        @Cleanup PreparedStatement preparedStatement = connection.prepareStatement(sql);

        @Cleanup ResultSet resultSet = preparedStatement.executeQuery();

        List<TodoVO> list = new ArrayList<>();

        while(resultSet.next()){
            TodoVO vo = TodoVO.builder()
                    .tno(resultSet.getLong("tno"))
                    .title(resultSet.getString("title"))
                    .dueDate(resultSet.getDate("dueDate").toLocalDate())
                    .finished(resultSet.getBoolean("finished"))
                    .build();

            list.add(vo);
        }
        return list;
        
 ~~~
**TestCode**
~~~java
    @Test
    public void testLine() throws Exception{
        List<TodoVO> list = todoDAO.selectAll();
        list.forEach(vo->System.out.println(vo));
    }
~~~

- TodoDAO의 조회 기능 구현

~~~java
public TodoVO selectOne(Long tno)throws Exception{
        String sql = "select * from tbl_todo where tno = ?";

        @Cleanup Connection connection = ConnectionUtil.INSTANCE.getConnection();
        @Cleanup PreparedStatement preparedStatement = connection.prepareStatement(sql);

        preparedStatement.setLong(1,tno);

        @Cleanup ResultSet resultSet = preparedStatement.executeQuery();

        resultSet.next();
        TodoVO vo = TodoVO.builder()
                .tno(resultSet.getLong("tno"))
                .title(resultSet.getString("title"))
                .dueDate(resultSet.getDate("dueDate").toLocalDate())
                .finished(resultSet.getBoolean("finished"))
                .build();

        return vo;
    }
~~~

**TestCode**
~~~java
@Test
    public void testSelectOne() throws Exception{
        Long tno = 4L;

        TodoVO vo = todoDAO.selectOne(tno);

        System.out.println(vo);
    }
~~~

- TodoDAO의 삭제/수정 기능 구현
~~~java
 public void deleteOne(Long tno) throws Exception{
        String sql = "delete from tbl_todo where tno = ?";

        @Cleanup Connection connection = ConnectionUtil.INSTANCE.getConnection();
        @Cleanup PreparedStatement preparedStatement = connection.prepareStatement(sql);

        preparedStatement.setLong(1, tno);

        preparedStatement.executeUpdate();
    }

    public void updateOne(TodoVO todoVO) throws Exception{
        String sql = "update tbl_todo set title = ?, dueDate = ?, finished = ? where tno =?";

        @Cleanup Connection connection = ConnectionUtil.INSTANCE.getConnection();
        @Cleanup PreparedStatement preparedStatement = connection.prepareStatement(sql);

        preparedStatement.setString(1, todoVO.getTitle());
        preparedStatement.setDate(2, Date.valueOf(todoVO.getDueDate()));
        preparedStatement.setBoolean(3, todoVO.isFinished());
        preparedStatement.setLong(4, todoVO.getTno());

        preparedStatement.executeUpdate();

    }
~~~
**TestCode**
~~~java
    @Test
    public void testSelectOne() throws Exception{
        Long tno = 1L;

        TodoVO vo = todoDAO.selectOne(tno);

        System.out.println(vo);
    }

    @Test
    public void testUpdateOne() throws Exception{
        TodoVO todoVO = TodoVO.builder()
                .tno(1L)
                .title("sample Title...")
                .dueDate(LocalDate.of(2023,05,20))
                .finished(true)
                .build();

        todoDAO.updateOne(todoVO);
    }
    
    
~~~


## MVC_JDBC 결합
TodoDAO를 구성하였으므로, 서비스 객체와 컨트롤러 객체를 연동하는 일만 남았다.
- DTO-> VO, VO -> DTO 형변환
  - ModelMapper 사용
~~~build.gradle
implementation group: 'org.modelmapper', name: 'modelmapper', version: '3.0.0'
~~~
- TodoVO.java
~~~
@Getter
@Builder
@ToString
@AllArgsConstructor
@NoArgsConstructor
public class TodoVO {
    private Long tno;
    private String title;
    private LocalDate dueDate;
    private boolean finished;
}
~~~
- MapperUtil.java
~~~java
public enum MapperUtil {
    INSTANCE;

    private ModelMapper modelMapper;

    MapperUtil(){
        this.modelMapper = new ModelMapper();
        this.modelMapper.getConfiguration()
                .setFieldMatchingEnabled(true)
                .setFieldAccessLevel(org.modelmapper.config.Configuration.AccessLevel.PRIVATE)
                .setMatchingStrategy(MatchingStrategies.STRICT);
    }

    public ModelMapper get(){
        return modelMapper;
    }
}
~~~
- TodoService
~~~java
public enum TodoService {
    INSTANCE ;

    private TodoDAO dao;
    private ModelMapper modelMapper;

    TodoService(){
        dao = new TodoDAO();
        modelMapper = MapperUtil.INSTANCE.get();
    }

    public void register(TodoDTO todoDTO) throws Exception{
        TodoVO todoVO = modelMapper.map(todoDTO, TodoVO.class);
        System.out.println("todoVO: "+ todoVO);
        dao.insert(todoVO);
    }
}
~~~

- Log4j2
  - 핵심 기능
    - 어펜더(Appender)
      - 로그를 어떤 방식으로 기록할 것인지
        - 콘솔창 출력
        - 파일 출력
        - 등  
    - 레벨(level)
      - 로그의 중요도 개념
      - 로그의 레벨 지정을 통해 해당 레벨 이상의 로그들만 출력할 수 있음.  
  - 추가
~~~build.gradle
    implementation group: 'org.apache.logging.log4j', name: 'log4j-core', version:'2.17.2'
    implementation group: 'org.apache.logging.log4j', name: 'log4j-api', version:'2.17.2'
    implementation group: 'org.apache.logging.log4j', name: 'log4j-slf4j-impl', version:'2.17.2'
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration status="WARN">
    <Appenders>
        <Console name ="Console" target="SYSTEM_OUT">
            <PatternLayout pattern ="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Root level ="info">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
~~~
- 용법
~~~java
@Log4j2 // @Log4j 아님!!

log.info(todoVO); 
//log.info(todoDTO);
~~~


## 컨트롤러와 서비스 객체 연동
- TodoService
~~~java
package com.zerock.jdbcex.service;

import com.zerock.jdbcex.dao.TodoDAO;
import com.zerock.jdbcex.domain.TodoVO;
import com.zerock.jdbcex.dto.TodoDTO;
import com.zerock.jdbcex.util.MapperUtil;
import lombok.extern.log4j.Log4j2;
import org.modelmapper.ModelMapper;

import java.util.List;
import java.util.stream.Collectors;

@Log4j2
public enum TodoService {
    INSTANCE ;

    private TodoDAO dao;
    private ModelMapper modelMapper;

    TodoService(){
        dao = new TodoDAO();
        modelMapper = MapperUtil.INSTANCE.get();
    }

    public void register(TodoDTO todoDTO) throws Exception{
        TodoVO todoVO = modelMapper.map(todoDTO, TodoVO.class);
//        System.out.println("todoVO: "+ todoVO);

        log.info(todoVO);
        dao.insert(todoVO);
    }

    public List<TodoDTO> listAll() throws Exception{

        List<TodoVO> voList = dao.selectAll();
        log.info(voList);

        List<TodoDTO> dtoList = voList.stream()
                .map(vo->modelMapper.map(vo,TodoDTO.class))
                .collect(Collectors.toList());

        return dtoList;
    }

    public TodoDTO get(Long tno) throws Exception{
        log.info("tno: "+tno);
        TodoVO todoVO = dao.selectOne(tno);
        TodoDTO todoDTO = modelMapper.map(todoVO,TodoDTO.class);
        return todoDTO;
    }
    public void remove(Long tno) throws Exception{
        log.info("tno: " + tno);
        dao.deleteOne(tno);
    }

    public void modify(TodoDTO todoDTO) throws Exception{
        log.info("todoDTO: " + todoDTO);
        TodoVO todoVO = modelMapper.map(todoDTO, TodoVO.class);
        dao.updateOne(todoVO);

    }
}
~~~
  - TodoListController
~~~java
package com.zerock.jdbcex.controller;

import com.zerock.jdbcex.dto.TodoDTO;
import com.zerock.jdbcex.service.TodoService;


import lombok.extern.log4j.Log4j2;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

@WebServlet(name ="todoListController", value = "/todo/list")
@Log4j2
public class TodoListController extends HttpServlet {
    private TodoService todoService = TodoService.INSTANCE;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        log.info("todo list............");

        try{
            List<TodoDTO> dtoList = todoService.listAll();
            req.setAttribute("dtoList", dtoList);
            req.getRequestDispatcher("/WEB-INF/todo/list.jsp").forward(req, resp);
        }catch (Exception e) {
            log.error(e.getMessage());
            throw new ServletException("list error");
        }
    }

}
~~~
  - TodoRegisterController
~~~java
package com.zerock.jdbcex.controller;

import com.zerock.jdbcex.dto.TodoDTO;
import com.zerock.jdbcex.service.TodoService;
import lombok.extern.log4j.Log4j2;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

@WebServlet(name = "todoRegisterController", value = "/todo/register")
@Log4j2
public class TodoRegisterController extends HttpServlet {
    private TodoService todoService = TodoService.INSTANCE;
    private final DateTimeFormatter DATEFORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd");

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
        log.info("/todo/register GET .....");
        req.getRequestDispatcher("/WEB-INF/todo/register.jsp").forward(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        TodoDTO todoDTO = TodoDTO.builder()
                .title(req.getParameter("title"))
                .dueDate(LocalDate.parse(req.getParameter("dueDate"),DATEFORMATTER))
                .build();
        log.info("/todo/register/register POST....");
        log.info(todoDTO);
        try{
            todoService.register(todoDTO);
        }catch(Exception e){
            e.printStackTrace();
        }
        resp.sendRedirect("/todo/list");
    }
}
~~~
  - TodoModifyController
~~~java
package com.zerock.jdbcex.controller;

import com.zerock.jdbcex.dto.TodoDTO;
import com.zerock.jdbcex.service.TodoService;
import lombok.extern.log4j.Log4j2;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

@WebServlet(name = "todoModifyController", value = "/todo/modify")
@Log4j2
public class TodoModifyController extends HttpServlet {
    private TodoService todoService = TodoService.INSTANCE;
    private final DateTimeFormatter DATEFORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd");

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        try{
            Long tno = Long.parseLong(req.getParameter("tno"));
            TodoDTO todoDTO = todoService.get(tno);
            //데이터 담기
            req.setAttribute("dto", todoDTO);
            req.getRequestDispatcher("/WEB-INF/todo/modify.jsp").forward(req, resp);
        }catch(Exception e){
            log.error(e.getMessage());
            throw new ServletException("modify get .... error");
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String finishedStr = req.getParameter("finished");

        TodoDTO todoDTO = TodoDTO.builder()
                .tno(Long.parseLong(req.getParameter("tno")))
                .title(req.getParameter(req.getParameter("title")))
                .dueDate(LocalDate.parse(req.getParameter("dueDate"), DATEFORMATTER))
                .finished(finishedStr != null && finishedStr.equals("on"))
                .build();

        log.info("/todo/modify POST.....");
        log.info(todoDTO);
        try{
            todoService.modify(todoDTO);
        }catch(Exception e){
            e.printStackTrace();
        }
        resp.sendRedirect("/todo/list");
    }
}
~~~
  - TodoRemoveController 
~~~java
package com.zerock.jdbcex.controller;

import com.zerock.jdbcex.service.TodoService;
import lombok.extern.log4j.Log4j2;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name ="todoRemoveController", value = "/todo/remove")
@Log4j2
public class TodoRemoveController extends HttpServlet {
    private TodoService todoService = TodoService.INSTANCE;

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Long tno = Long.parseLong(req.getParameter("tno"));
        log.info("tno: "+tno);
        try{
            todoService.remove(tno);
        }catch(Exception e){
            log.error(e.getMessage());
            throw new ServletException("read error");
        }
        resp.sendRedirect("/todo/list");
    }
}
~~~

  - TodoReadController 
~~~java
package com.zerock.jdbcex.controller;

import com.zerock.jdbcex.dto.TodoDTO;
import com.zerock.jdbcex.service.TodoService;
import lombok.extern.log4j.Log4j2;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "todoReadController", value = "/todo/read")
@Log4j2
public class TodoReadController extends HttpServlet {
    private TodoService todoService = TodoService.INSTANCE;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        try{
            Long tno = Long.parseLong(req.getParameter("tno"));
            TodoDTO todoDTO = todoService.get(tno);

            //데이터 담기
            req.setAttribute("dto", todoDTO);
            req.getRequestDispatcher("/WEB-INF/todo/read.jsp").forward(req, resp);
        } catch (Exception e) {
            log.error(e.getMessage());
            throw new ServletException("read error");
        }
    }
}
~~~
![IMG_7133](https://github.com/HJC96/WebDev/assets/87226129/a823264f-4d69-4a2d-b568-22ceaf3dde6d)


