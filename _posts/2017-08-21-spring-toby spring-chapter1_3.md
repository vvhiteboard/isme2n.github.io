# Chapter1-3

## 목록

---

-  프레임워크와 라이브러리
-  @Scope (Bean Scope)



## 프레임워크와 라이브러리

라이브러리를 사용하는 어플리케이션의 코드는 어플리케이션의 흐름을 직접 제어한다.
프레임워크는 어플리케이션 코드가 프레임워크에 의해 사용된다.

즉, 라이브러리는 이미 만들어진 코드를 직접 사용하여 어플리케이션 코드를 작성하는 것이고 프레임워크는 내가 만든 어플리케이션 코드를 프레임워크가 가져가서 사용하는 것이다.



## @Scope

@Scope는 Bean을 등록할 때 해당 클래스의 scope를 지정하는데 사용하는 어노테이션이다.
어노테이션에 프로퍼티로 해당 Bean의 범위를 지정할 수 있다.



**[예제]**

```java
@Scope("prototype")
@Bean
public class TestBean {
  // ...
}
```



- singleton (기본값)
  하나의 빈 정의가 스프링 IoC 컨테이너에 하나의 인스턴스가 생성되는 범위
- prototype
  하나의 빈 정의가 다수의 객체 인스턴스가 되는 범위
- request
  하나의 빈 정의가 하나의 HTTP 요청의 라이프 사이클이 되는 범위. ApplicationContext 범위에서 유효하다.
  Spring MVC WebApplication에서 사용
- session
  하나의 빈 정의가 하나의 HTTP Session의 라이프 사이클이 되는 범위. ApplicationContext 범위에서 유효하다.
  Spring MVC WebApplication에서 사용
- global session
  하나의 빈 정의가 전역적인 HTTP Session의 라이프 사이클이 되는 범위. Portlet 컨텍스트와 ApplicationContext에서 사용할 때 유효하다.
  Spring MVC WebApplication에서 사용