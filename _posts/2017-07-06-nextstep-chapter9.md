---
layout: post
title:  "Chapter 9"
categories: nextStep
tags: jekyll blog theme devlog
---

### 모르는것

---

-  WebServer의 초기화 과정
-  소스코드의 호출 순서 및 흐름
-  한글이 깨지는 현상을 ServletFilter로 해결
-  @WebServlet loadOnStartUp과 URL 매핑
   -  loadOnStartUp이 의미하는것?
   -  URL매핑중 "/"와 "/*"의 차이
-  서블릿을 잘못 구현한 경우 (동시성 문제)
   -  책에서 추천해준 영상 보기
-  조합이란? (composition)
-  ModelAndView에서 render 메서드의 역할은?




### 알아본 것

---

-  WebServer의 초기화 과정
   -  서블릿 컨테이너(톰캣)이 실행되면서 등록된 이벤트 리스너에 정의된 ServletContextListener의 contextInitialized() 메서드를 호출한다.
   -  DispatcherServlet 인스턴스를 생성한다. (생성자 호출)
   -  @WebServlet 어노테이션에 loadOnStartup 속성에 의해 DispatcherServlet 인스턴스를 생성한다.
      -  0 또는 음수 : 실제 요청이 들어왔을 때 인스턴스를 생성한다.
      -  양수 : 초기화 과정에서 인스턴스를 생성한다. (숫자가 작은 인스턴스부터 생성 ex) 1 -> 2 )
      -  미지정 : 0으로 설정한 것과 같음
   -  DispatcherServlet 인스턴스가 생성되면 해당 인스턴스의 init() 메서드가 호출된다.
   -  init() 메서드에서 RequestMapping 객체를 생성한다
   -  RequestMapping 인스턴스의 initMapping() 메서드가 호출된다.
      -  각 요청 url별로 Controller 인스턴스를 매핑한다.



-  Filter는 doFilter를 실행하여 필터를 적용한다
   -  ResourceFilter : 정적 자원 요청인 경우 서블릿에 위임하지 않고 바로 자원을 제공한다.
      -  url 패턴 : "/"와 "/*"의 차이
         -  "/" : 디폴트 서블릿으로 서블릿 매핑 URL에 걸러지지 않은 URL에 대한 패턴이다.
         -  "/*" : 모든 URL에 대한 패턴으로 서블릿 매핑 URL에 걸러졌더라도 적용된다.
   -  CharacterEncodingFilter
      -  HttpServletRequest에 인코딩 설정이 안되어 있으면 기본 인코딩 설정을 한다.
      -  강제 설정이 setup 된 경우 Request와 Response 인코딩을 강제로 설정해준다.



-  ModelAndView에서 render 메서드의 역할

   -  Controller에서 역할에 맞는 View를 리턴

      -  JsonView 혹은 JspView (두 View객체는 ModelAndView를 상속)
      -  ModelAndView (추상 클래스 혹은 Interface)는 render 메서드를 구현

   -  DispatcherServlet에서 Controller의 리턴을 받아 render 메서드를 호출해준다.

   -  해당 View가 render됨

   >  자세한 내용은 8장 View 부분을 참고하기 바람

   예제코드) 

   ```java
   // ModelAndView
   public interface ModelAndView {
     public void render(); // render 메서드 정의
   }

   // JspView
   public class JsonView implements ModelAndView {
     public void render() {
       //... render 메서드 구현
     }
   }

   // Controller
   public class TestController {
     public ModelAndView execute() {
       // ...
       return new JspView(Model model); // JspView를 리턴
     }
   }

   // DispatcherServlet
   public void service() {
     // ...
     Controller controller = dispathcerMap.get(url);
     ModelAndView view = controller.execute();
     view.render(); // controller에서 리턴한 ModelAndView의 render 메서드 호출
   }
   ```




-  상속 vs 조합 (Inheritance VS Composition)
   -  참고 : https://wikidocs.net/894
   -  상속
      -  부모 클래스를 자식 클래스가 그대로 상속 받는다.
      -  장점 : 컴파일 시점에 정적으로 정의되기 때문에 이해하기 쉽다.
      -  단점 : 컴파일 시점에 정의되기 때문에 유연성이 떨어진다.
   -  조합
      -  다른 여러개의 객체를 포함해 새로운 기능 혹은 객체를 구성하는 것
      -  장점
         -  동적으로 정의되기 때문에 유연하게 동작함
         -  완벽한 재사용성
      -  단점 : 동적으로 정의되기 때문에 코드 이해가 어려움



-  서블릿을 잘못 구현한 경우 동시성 문제
   -  관련 영상 : https://youtu.be/9lQsAPFQjBg









