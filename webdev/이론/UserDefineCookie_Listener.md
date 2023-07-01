### 쿠키의 생성과 전송
- 사용자 정의 쿠키
  - newCookie() 를 이용하여 생성
  - 문자열로된 이름과 값이 필요
  - 값은 일반적인 문자열로 저장이 안되어서, URLEncoding으로 저장


쿠키를 사용하는 경우
- 쿠키는 서버와 브라우저 사이를 오가기 때문에 보안에 취약하기 때문에 제한적인 용도로 사용이 필요하다.
- '오늘 하루 이 창 열지 않기'나 '최근 본 상품 목록'과 같이 조금 사소하고 서버에서 보관할 필요가 없는 데이터들을 처리.



### 리스너
옵저버 패턴
- 구독(subscribe)하는 객체를 보관하고 있다가 변화가 발생하면 구독 객체들을 실행(publish)

ServletContextListener
- 해당 프로젝트가 실행되자마자 실행되었으면 하는 작업

~~~java
package org.zerock.jdbcex.listener;

import lombok.extern.log4j.Log4j2;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

@WebListener
@Log4j2
public class W2AppListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        log.info("-------------------------------");
        log.info("-------------------------------");
        log.info("-------------------------------");

        ServletContext servletContext = sce.getServletContext();
        servletContext.setAttribute("appName", "W2");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        log.info("-------------------------------");
        log.info("-------------------------------");
        log.info("-------------------------------");
    }
}

~~~

세션 관련 리스너
- HttpSessionLister 혹은 HttpSessionAttriubuteListener
  - HttpSession이 생성되거나 setAttribute()등이 작업이 이루어질때 이를 감지할 수 있다. 
~~~java
package org.zerock.jdbcex.listener;

import lombok.extern.log4j.Log4j2;

import javax.servlet.annotation.WebListener;
import javax.servlet.http.HttpSessionAttributeListener;
import javax.servlet.http.HttpSessionBindingEvent;

@WebListener
@Log4j2
public class LoginListener implements HttpSessionAttributeListener {

    @Override
    public void attributeAdded(HttpSessionBindingEvent event) {
        String name = event.getName();

        Object obj = event.getValue();

        if(name.equals("loginInfo")){
            log.info("A user logined......");
            log.info(obj);
        }
    }
}

~~~
