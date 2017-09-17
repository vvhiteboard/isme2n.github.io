---
layout: post
title:  "Chapter 2-3"
categories: web
tags: spring
---

-  @RunWith
-  @ContextConfiguration
-  @DirtiesContext
-  DI를 적용해야하는 이유
-  침투적 / 비침투적 기술

> (p183 ~ p196)

<br>

<br>

## @RunWith

>  참고 : http://junit.sourceforge.net/javadoc/org/junit/runner/RunWith.html

<br>

@RunWith또는 @RunWith를 확장한 어노테이션이 붙은 클래스는 JUnit의 기능을 확장한 클래스를 사용한다. 
JUnit의 기능을 확장한 클래스를 @RunWith에 명시하면 확장한 클래스를 이용하여 테스트를 수행한다.

@Runwith는 다음과 같이 정의되어 있다.

<br>

**[정의]**

```java
@Retention(value=RUNTIME)
@Target(value=TYPE)
@Inherited
public @interface RunWith
```

<br>

@RunWith 어노테이션을 붙인 테스트 클래스는 JUnit 기본 클래스가 아닌 명시한 JUnit 클래스에서 테스트를 수행한다. 아래에 Spring에서 제공하는 Runner 클래스를 명시한 예제가 있다.

<br>

**[예제]**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "/context.xml")
public class TestClass() {
    //...
}
```

위와 같이 설정하면 JUnit을 확장구현한 SpringJUnit4ClassRunner 클래스에서 제공하는 테스트 기능을 사용하여 테스트 코드를 실행할 수 있다. 만약 스프링에서 제공하는 기능을 테스트 하고 싶거나 ApplicationContext를 로드 해서 테스트를 해야하는 경우에 해당 Runner를 사용하면 테스트하기 위한 환경을 제공한다.

또한 **@ContextConfiguration** 어노테이션을 이용하면 테스트를 위한 설정을 로드 할 수 있다. 테스트 코드가 의존하는 ApplicationContext 설정이 있는 경우 이 어노테이션 설정을 통해 의존하는 Context를 로드할 수 있다. @ContextConfiguration 어노테이션에서 `locations` 설정을 이용하면 해당 위치에 xml 설정 파일을 로드하여 해당 설정에 맞는 테스트 Context를 생성한다. 이를 이용하여 테스트 코드를 실행할 수 있다.

여기서 생성되는 ApplicationContext는 같은 테스트 클래스 내에서 서로 공유된다. 즉 위 예제를 예로 들면 TestClass내에 있는 모든 메소드들은 같은 ApplicationContext를 공유하게 된다. 따라서 하나의 테스트 메소드에서 해당 ApplicationContext를 변경하게 되면 모든 테스트 메소드들이 영향을 받는다.

이런 문제점을 해결하기 위한 것이 바로 **@DirtiesContext** 어노테이션이다.

@DirtiesContext 어노테이션은 해당 클래스 혹은 메소드가 ApplicationContext를 변경한다는 뜻이다. 즉 해당 클래스/메소드가 실행된 후에 ApplicationContext를 다시 로드한다.

만약 @DirtiesContext가 **클래스 이름 위에 적용**되어 있으면 해당 클래스에 있는 각 **테스트 메소드가 수행 될 때 마다** ApplicationContext를 다시 로드한다. 이런 경우 각 테스트 메소드를 실행할 때 마다 ApplicationContext를 로드하므로 굉장히 느리다. 따라서 될 수 있으면 클래스 위에 적용하지 않도록 한다.

위와 같은 문제를 해결하려면 @DirtiesContext를 **메소드 위에 적용**하면 된다. 메소드 위에 적용하면 **해당 테스트 메소드가 실행된 후에만 ApplicationContext를 다시 로드** 하기 때문에 비교적 빠르다.

<br>

**[예제]**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "/context.xml")
@DirtiesContext
public class TestClass1() {
  	// @DirtiesContext 어노테이션을 클래스에 적용하면
    // TestClass1 내에 있는 각 테스트 메소드를 실행할 때마다
  	// ApplicationContext를 다시 로드 한다.
  
  	// 테스트 메소드들...
}


@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "/context.xml")
public class TestClass2() {
  	// @DirtiesContext 어노테이션을 메소드에 적용하면
    // test 메소드가 실행된 후에만
  	// ApplicationContext를 다시 로드 한다.
  
	@DirtiesContext
	public void test() {
        // 테스트 코드...
    }
}
```

<br>

<br>

## DI를 적용해야하는 이유

DI(Dependency Injection)기법은 많은 장점을 가지고 있다. 하지만 그렇다고해서 꼭 DI를 적용해야 할까? 만약 우리가 만들고 있는 소프트웨어를 만들때 DB서버가 하나 밖에 없기 때문에 하나의 DataSource만 사용하고 굳이 여러 개의 DataSource를 사용할걸 대비해서 DI를 적용하지 않는다고 하자. 하나의 DataSource만을 사용하는 경우엔 DI를 굳이 적용하지 않아도 될 것만 같은 느낌이 든다. 하지만 그렇지 않다.

책(토비의 스프링)에서는 그렇지 않은 이유를 크게 3가지로 정리해볼 수 있다.

첫째. 소프트웨어 개발에서 절대 바뀌지 않는 것은 없다.

소프트웨어는 언젠가 반드시 변경될 것이다. 그렇다면 현재는 불필요하더라도 나중을 위해 확장하기 용이하도록 설계하는 것이 좋을 것이다. 그런 이유로 확장성이 좋은 DI를 적용해야 하는 것이다. 그리고 DI를 적용하는것이 그렇게 많은 자원을 필요로 하는 작업이 아니다. 즉 적용 했을 때와 적용하지 않았을 때에 드는 시간이나 비용이 그리 크게 차이 나지 않는다. 그렇다면 굳이 적용하지 않을 이유가 없다는 것이다. 비용 차이가 크지 않기 때문에 나중에 기능을 추가하거나 수정하게 될 때를 대비해서 DI를 적용하는 것이 훨씬 이득이다.

<br>

둘째. 추후 기능 추가에 용이하다.

셋째. 테스트 작성에 용이하다.

<br>

## 침투적 / 비침투적 기술

침투적 기술이란 프레임 워크 기술과 관련된 내용이 코드나 규약에 나타나는 것을 말한다. 쉽게 말하면 특정 프레임 워크를 사용할 때 해당 프레임 워크에서 제공하는 규약에 따라 코드를 작성해야 하는 것을 말한다. 대표적으로 EJB가 침투적 프레임 워크라고 할 수 있다.

비침투적 기술이란 침투적 기술과 반대로 프레임 워크 기술과 관련된 내용이 코드나 규약에 나타나지 않는 것을 말한다. 즉, 개발하는데 있어서 코드 작성에 규약이 없는 프레임 워크를 말한다. 대표적인 비침투적 프레임 워크로 스프링이 있다.