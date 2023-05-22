### MyBatis
- SQL의 실행결과를 객체지향으로 매핑
- 사용 방식
  - MyBatis를 단독으로 개발하고 스프링에서 DAO를 작성해서 처리하는 방식
  - MyBatis와 스프링을 연동하고 Mapper 인터페이스만 이용하는 방식

### Mapper 인터페이스 활용하기
TimeMapper
- 데이터베이스의 현재 시각을 문자열로 처리하도록 구성
~~~java
package org.zerock.springex.mapper;

import org.apache.ibatis.annotations.Select;

public interface TimeMapper {
    @Select("select now()")
    String getTime();
}
~~~

~~~java
package org.zerock.springex.sample;
import org.junit.jupiter.api.Test;

import lombok.extern.log4j.Log4j2;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.zerock.springex.mapper.TimeMapper;

@Log4j2
@ExtendWith(SpringExtension.class)
@ContextConfiguration(locations = "file:src/main/webapp/WEB-INF/root-context.xml")
public class TimerMapperTests {

    @Autowired(required = false)
    private TimeMapper timeMapper;

    @Test
    public void testGetTime(){
        log.info(timeMapper.getTime());
    }
}

~~~
