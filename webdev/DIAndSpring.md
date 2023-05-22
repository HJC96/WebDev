## DI(의존성 주입)
- 목적
  - 객체와 객체 간의 관계를 더욱 유연하게 유지
- 방법
  - 스프링 프레임워크를 통한
    - XML 설정 
    - 자바 설정

~~~java
package org.zerock.springex.sample;

import lombok.extern.log4j.Log4j2;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;


@Log4j2
@ExtendWith(SpringExtension.class)
@ContextConfiguration(locations = "file:src/main/webapp/WEB-INF/root-context.xml")
public class SampleTests {

    @Autowired // 만일 해당 타입의 빈(Bean)이 존재하면 여기 주입
    private SampleService sampleService;

    @Test
    public void testService1(){
        log.info(sampleService);
        Assertions.assertNotNull(sampleService);
    }
}
~~~


## 스프링 프레임워크 어노테이션
- @Autowired: 필요한 타입의 필드 주입
- @Controller: MVC의 컨트롤러를 위한 어노테이션
- @Service: 서비스 계층의 객체를 위한 어노테이션
- @Repository: DAO같은 객체를 위한 어노테이션
- @Component: 일반 객체나 유틸리티 객체를 위한 어노테이션
- @Primary: 객체가 여러개일때 통해 하나를 지정해줄 수 있는 어노테이션
- @Qualifier: 이름을 지정해서 특정한 이름의 객체를 주입받는 어노테이션


SampleDAO 주입
~~~java
package org.zerock.springex.sample;

import lombok.ToString;
import org.springframework.beans.factory.annotation.Autowired;

@ToString
public class SampleService {

    @Autowired
    private SampleDAO sampleDAO;
}
~~~

## ApplicationContext와 Bean
스프링을 이용할때는 클래스를 작성하거나 객체를 직접 생성하지 않는다. 스프링 내부에서 ApplicationContext가 생성된 객체들을 관리한다.
- ApplicationContext
  - Bean이라고 부르는 객체들을 관리하기 위해 필요 


<img width="1800" alt="image" src="https://github.com/HJC96/WebDev/assets/87226129/c4465072-35df-4711-8f08-9da7645e8397">

~~~java
package org.zerock.springex.sample;

@Repository
public class SampleDAO {
}
~~~


~~~java
package org.zerock.springex.sample;

import lombok.ToString;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
@ToString
public class SampleService {

    @Autowired
    private SampleDAO sampleDAO;
}
~~~


### 생성자 주입 방식
- 주입 받아야 하는 객체의 변수는 final로 지정
- 생성자를 이용해서 해당 변수를 생성자의 파라미터로 지정

~~~java
package org.zerock.springex.sample;

import lombok.RequiredArgsConstructor;
import lombok.ToString;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service
@ToString
@RequiredArgsConstructor
public class SampleService {

    private final SampleDAO sampleDAO;
}
~~~


