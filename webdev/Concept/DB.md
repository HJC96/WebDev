

JPA 사용 이유?
- 기존 SQL 중심적인 개ㅌ$발은 유지보수가 어렵다는 단점이 있다.

~~~java
public class Member{
  private String memberId;
  private String name;
...
}
~~~

~~~sql
INSERT INTO MEMBER(MEMBER_ID, NAME) VALUES
SELECT MEMBER_ID, NAME FROM ...
~~~

만약 TEL 필드가 하나 추가되면???
-> sql문을 전부 TEL을 추가하는 형태로 바꿔줘야 한다.


JPA는 애플리케이션과 JDBC 사이에서 동작하며, 유저에게 추상화가 잘되어있다.
<img width="620" alt="image" src="https://github.com/HJC96/WebDev/assets/87226129/66df36bf-49b3-46cf-888f-7e8252c8c91f">

하지만 엄청 디테일한 구현은 어렵다고 들음...
-> 이거에 대해서 정리하자
