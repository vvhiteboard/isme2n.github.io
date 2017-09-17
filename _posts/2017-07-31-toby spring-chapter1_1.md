---
layout: post
title:  "Chapter 1-1"
categories: web
tags: spring
---

-  JavaBean 규약
-  팩토리 메소드 패턴


<br>

### 자바빈(JavaBean) 규약

---

<br>

자바 빈이란 특정 설계 규약을 지키는 자바 클래스를 의미한다. 자바 빈 규약은 이 자바 빈의 설계 규약을 말한다.

자바 빈은 JSP, EJB등에서 사용하고 있고 우리가 Spring에서 일반적으로 사용하는 Model 객체가 이에 속한다.



**[JavaBean 규약]**

-  접근 제어자
   -  Class : public
   -  멤버변수 : private
   -  생성자 : public
   -  getter/setter : public


-  멤버변수마다 getter/setter가 있어야 한다.
   -  get 메소드는 파라미터가 없어야 한다.
   -  set 메소드는 반드시 하나 이상의 파라미터가 있어야 한다.
-  기본 생성자가 있어야 한다.


<br>



**[예제코드]**

```java
public class User {
  private String name;
  private Date birth;
  private int age;
  
  // 기본으로 생성되므로 생략해도 됨
  // User() {}
  
  public String getName() {
    return this.name;
  }
  
  public void setName(String name) {
    this.name = name;
  }
  
  public Date getBirth() {
    return this.birth;
  }
  
  public void setBirth(Date birth) {
    this.birth = birth;
  }
  
  public int getAge() {
    return this.age;
  }
  
  public void setAge(int age) {
    this.age = age;
  }
}
```

<br>

### 팩토리 메소드 패턴

---

<br>

템플릿 메소드 패턴의 일종으로 팩토리 메소드 패턴은 객체를 생성해서 리턴하는 클래스 디자인 패턴이다.

이전에 알아봤던 템플릿 메소드 패턴은 상위 추상 클래스에서 명세해 놓은 메소드를 하위 메소드에서 구현하여 하위 메소드 별로 각각의 기능을 구현하기 위해서 사용하는 패턴이다.

팩토리 메소드 패턴은 하위 클래스가 구현하는 기능이 어떤 객체를 생성하여 리턴하는 기능을 구현하는 경우에 적합한 디자인 패턴이다.

단, 팩토리 메소드 패턴에서 하위 클래스가 리턴하는 클래스는 상위 클래스는 알지 못해도 된다.

예제코드를 보면서 설명하면 다음 같다.



**[예제코드]**

**ConnectionFactory 클래스**

```java
public abstract class ConnectionFactory {
 public abstract Connection create();
}
```



**AConnectionFactory 클래스**

```java
public class AConnectionFactory extends ConnectionFactory {
 @Override
 public Connection create() {
  return new AConnection();
 }
}

class AConnection() extends Connection {
  @Override
  public void doConnect();
}
```



**BConnectionFactory 클래스**

```java
public class BConnectionFactory extends ConnectionFactory {
 @Override
 public Connection create() {
  return new BConnection();
 }
}

class BConnection() extends Connection {
  @Override
  public void doConnect();
}
```

<br>

위와 같이 3가지 클래스가 있다고 하자.

ConnectionFactory는 추상 클래스로 Connection 객체를 반환하는 create 메소드를 추상 메소드로 가지고 있다. 

그리고 각각 AConnectionFactory와 BConnectionFactory가 있다. 각각은 Connection을 상속 받은 AConnection과 BConnection을 반환한다.

이 ConnectionFactory는 아래와 같이 사용할 수 있다.

<br>

**Main 클래스**

```java
public class Main {
  ConnectionFactory factory;
  
  public void setFactory(ConnectionFactory factory) {
    this.factory = factory;
  }
  
  // 외부에서 어떤 ConnectionFactory를 주입해주는가에 따라 리턴되는 Connection이 달라지지만
  // Main 클래스는 그 객체가 어떤 건지 관심이 없다.
  public void main() {
    Connection conn = this.factory.create();
    conn.doConnect();
  } 
}

// ex1) Main.setFactory(new AConnectionFactory());
// ex2) Main.setFactory(new BConnectionFactory());

```



위의 Main 클래스는 어떤 ConnectionFactory를 쓰는지 그 Factory 객체가 어떤 Connection을 리턴하는지 관심이 없다. 오로지 Connection을 생성하여 doConnect() 메소드를 호출 할뿐.

위와 같이 상위 클래스에서 리턴하는 객체는 하위 클래스의 구현에 따라 달라지지만 상위 클래스는 그것이 어떤 객체인지 알지 못하도록 하는 디자인 패턴이 팩토리 메소드 패턴이다.