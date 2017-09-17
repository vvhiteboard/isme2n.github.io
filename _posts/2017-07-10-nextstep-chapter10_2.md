---
layout: post
title:  "Chapter 10-2"
categories: web
tags: nextstep
---

## URI와 URL 

URI = URL + URN

참고1 : https://blog.lael.be/post/61

참고2 : http://sunychoi.github.io/java/2015/04/27/uri-url.html



### URL

Uniform Resource Locator

예전에는 웹 자원이라고 하면 보통 파일을 의미했다. (보통은 html파일)

따라서  웹에서 특정 자원(파일)의 위치를 나타내는 것을 **URL**이라고 했다.

예를 들면 http://wiki.org/web/url/data 이라는 웹 페이지가 있다면

이것은 wiki.org라는 호스트 서버에 web/url 디렉터리에 data이라는 자원(파일)이 있다는 뜻이다.



### URI

Uniform Resource Identifier

요즘에는 특정 파일을 웹 자원으로 하지 않고 Apache, Tomcat 등의 웹 서버(웹 어플리케이션 서버 WAS)를 통해서 자원이 제공된다.

따라서 웹에서 특정 자원의 식별자를 나타내는 것을 **URI**라고 한다.

예를 들면 http://wiki.org/web/uri/data?target=URI 라는 웹 페이지가 있다면

여기서 wiki.org라는 호스트 서버에 /web/uri/data 라는 자원이 있는데

원하는 데이터를 보기 위해선 ?target=URL 까지 필요하다

여기서 /web/uri/data?target=URI 를 URI라고 할 수 있다.





### URL과 URI의 차이

URL은 단순히 자원의 위치를 나타내는 용어이다.

위에서 예를 든것 처럼 URL은 특정 자원의 위치를 나타내는 것이다.

http://wiki.org/web/url/data로 접속하면 누구에게나 같은 자원을 제공할 것이다.

따라서 위의 경로는 URL이면서 URI 이다.

하지만 http://wiki.org/web/uri/data?target=URI 의 경우에는 target에 따라서 다른 자원을 제공해 줄것이다. (일반적인 웹 어플리케이션의 경우)

위 경우에 URL 주소는 http://wiki.org/web/uri/data 이고 http://wiki.org/web/uri/data?target=URI 은 URL이 아니다.

또 두가지 경우 모두 URI는 될 수 있다.





## Reflections / ReflectionUtils

### Reflections





### ReflectionUtils

일반적인 Utils 클래스와 동일하게 Reflection과 관련된 기능을 제공하는 Utils 클래스다.

자세한 사용법과 메서드는 필요할때 직접 찾아보는 걸로...





### basePackage의 역할

Reflections 객체를 통해 접근할 수 있는 package 목록을 지정한다.



```java
Reflections reflection = new Reflections("com.demo.user", "com.demo.controller");
```



위 예제코드와 같이 Reflections 생성자에 패키지 리스트를 넘기면 해당 패키지내에 포함된 클래스에 접근이 가능하다.

즉 basePackage는 Reflections 객체가 접근할 수 있는 패키지를 명시해주는 것이라고 생각하면 된다.





## Adapter Pattern

참고 : http://choipattern.blogspot.kr/2013/08/adapter.html

어댑터 패턴이란 이미 제공되고 있는 기능을 그대로 사용하기 어려울 때 그것을 활용하여 필요한 기능을 구현하여 제공하는 형태의 클래스 패턴을 말한다.

가장 간단한 예를 들자면 linked list를 이용하여 stack을 구현하는 것입니다.

"이미 제공되고 있는" linked list 기능을 활용하여 "필요한" stack을 구현하는 것입니다.

어뎁터 패턴은 크기 클래스에 의한 어댑터 패턴과 인스턴스에 의한 어댑터 패턴이 있습니다.

(추가적인 내용은 링크 참고)



