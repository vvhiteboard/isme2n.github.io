---
layout: post
title:  "Chapter 10-1"
categories: web
tags: nextstep
---

## Annotation Define 

예제 코드

```java
import java.lang.annotation.ElementType
import java.lang.annotation.Retention
import java.lang.annotation.RetentionPolicy
import java.lang.annotation.Target
  
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Controller {
  String value() default "";
}

//....

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface RequestMapping {
  String value() default "";
  
  RequestMethod method() default RequestMethod.GET;
}
```

Controller @interface 코드를 예로 들면

value라는 변수를 입력받을 수 있고 default는 ""로 입력할 수 있게 정의한것이다.

`@Controller(value = "temp")`와 같이 사용할 수 있고 value를 지정하지 않으면 default 값인 ""이 저장될 것이다.



-  @Target Annotation

   -  어노테이션을 지정할 수 있는 타입을 설정

   -  Enum형인 ElementType를 이용하여 지정할 수 있다.

      -  PACKAGE : 패키지 선언시
      -  TYPE : 타입 선언시 
      -  CONSTRUCTOR : 생성자 선언시
      -  FIELD : 멤버 변수 선언시
      -  METHOD : 메소드 선언시
      -  ANNOTATION_TYPE : 어노테이션 타입 선언시
      -  LOCAL_VARIABLE : 지역변수 선언시
      -  PARAMETER : 파라미터 선언 시 ex) `@RequestParam` or `@Params` 등..
      -  TYPE_PARAMETER : 파라미터 타입 선언 시 `List<@NotNull String> arr = ...;`
      -  TYPE_USE : 타입 사용시

      ​

-  @Retention Annotation

   -  어노테이션의 scope를 나타낸다.
   -  Enum형인 RetentionPolicy를 이용하여 어노테이션의 범위를 지정할 수 있다.
   -  RetentionPolicy
      -  RUNTIME : 런타임 중에도 JVM에 의해 참조가 가능하다.
      -  CLASS : 컴파일 시 사용되고 .class 파일에 저장된다. 런타임시 사용하지 않음
      -  SOURCE : 컴파일시까지만 사용되고 .class 파일에 저장되지 않는다.





## Java reflection (자바 리플렉션)

**자바 객체를 통해 클래스 정보를 알아내는 기법**

ex)

`Object obj = new String();`

인스턴스는 String 인스턴스지만 Object형에 담았기 때문에 String에 있는 메서드나 멤버 변수는 사용할 수 없다.

위와 같은 경우에 자바 리플렉션 기법을 활용하면 인스턴스 객체를 가지고 클래스 정보를 알아낸 후 해당 인스턴스의 메서드를 호출 하거나 멤버 변수에 접근할 수 있다.



여러가지 API들과 메서드들 정리

-  java.lang.Class API

   -  getFields() : 해당 클래스의 public 멤버 변수를 조회
   -  getDeclaredFields() : 해당 클래스의 모든 멤버 변수를 조회
   -  getConstructors() : 해당 클래스의 public 생성자를 조회
   -  getDeclaredConstructors() : 해당 클래스의 모든 생성자를 조회
   -  getMethods() : 해당 클래스의 public 메서드를 조회
   -  getDeclaredMethods() : 해당 클래스의 모든 메서드를 조회

-  method.invoke

   -  Method 클래스의 invoke 메서드는 invoke 메서드 인자로 넘어온 인스턴스의 해당 메서드가 있으면 그 메서드를 호출한다.

-  method.isAnnotationPresent(Class clazz)

   -  해당 clazz Class에 어노테이션이 있는지 확인하는 메서드

-  clazz.newInstance()

   -  기본 생성자를 호출해준다. 기본 생성자가 없으면 호출할 수 없다.
   -  기본 생성자가 없는 경우 getDeclaredConstructors()를 통해 생성자를 찾을 수 있다.
   -  constructor.newInstance(Object... args)로 인스턴스를 생성한다.

-  필드 접근

   -  getDeclaredField(String name)을 이용하여 private 필드를 찾는다.

   -  private 필드에 접근하기 위해선 접근 권한을 수정해야한다

      -  `Field.setAccessible(true)`
      -  setAccessible을 설정하면 Field 클래스에 Override 속성이 true로 변경됨

   -  `field.set(student, "테스트")`와 같이 private 필드에 값을 할당할 수 있다.

   -  예제코드

      ```java
      @Test
       public void test1() throws IllegalAccessException, InstantiationException {
        TranslationParam translationParam = new TranslationParam() {{
         setSourceLanguage("en");
         setTargetLanguage("ko");
         setQuery("hello");
        }};

        Class test = TranslationParam.class;
        TranslationParam param = (TranslationParam) test.newInstance();

        Method[] methods = test.getMethods();

        Field[] fields = test.getDeclaredFields();

        fields[0].set(param, "test0"); // public 필드는 바로 set 가능
        // fields[1].set(param, "ddddd"); // private 필드는 set하려고하면 IllegalAccessException 발생
        fields[1].setAccessible(true); // Override 속성을 true로 변경해야 함
        fields[1].set(param, "test1");
       }
      ```

      ​







