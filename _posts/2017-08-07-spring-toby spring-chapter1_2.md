# Chapter1-2

## 목록

---

-  객체지향 설계원칙 (SOLID)
-  전략 패턴 (Strategy Pattern)


<br>

## 객체지향 설계원칙 (SOLID)

---

객체지향 디자인 원리들을 이용하면 더 유지보수하기 쉽고, 유연하고, 확장이 쉬운 소프트웨어를 만들 수 있다.

<br>

### SRP (Single Responsibility Principle)

>  단일 책임의 원칙

작성된 클래스는 하나의 기능만을 제공해야 하며 클래스의 책임은 해당 기능을 수행하는데 집중해야 한다는 원칙이다. SRP 원칙을 적용하면 책임 영역이 확실해지기 때문에 변경이 필요한 경우 다른 영역에 영향이 가지 않도록 할 수 있다.

<br>

**[적용방법]**

-  공통적인 부분을 새로운 클래스로 추출하여 부모 클래스로 추출하고 하위 클래스는 각 하위 클래스에 필요한 기능만 남겨두도록 한다.
-  여기저기 흩어져있는 기능을 하나의 클래스로 모아 응집도를 높인다.



**[예제코드]**

```java
public class Computer {
  private String productNo;
  private String price;
  private String spec;
  private String model;
  // setter & getter
}

public class Monitor {
  private String productNo;
  private String price;
  private String screen;
  // setter & getter
}
```

위 코드에서 공통적인 부분을 하나의 클래스로 추출하고 각 하위 클래스에 있어야하는 기능은 하위 클래스에 포함한다.

<br>

```java
public class Product {
  private String productNo;
  private String price;
  //...
}

public class Computer extends Product {
  private String spec;
  private String model;
  //...
}

public class Monitor extends Product {
  private String screen;
  //...
}
```

위와 같이 상위 클래스인 Product로 추출할 수 있다. 이렇게 하면 상위 클래스는 물건의 번호, 가격에 집중할 수 있고 각 컴퓨터와 모니터는 자신들에게 주어진 정보/기능만 제공하는데 집중할 수 있다.

물론 위의 코드가 좋은 코드라고 할 수는 없지만 단일 책임 원칙을 이해하는데 부족함이 없다.

<br>

### OCP (Open-Closed Principle)

>  개방폐쇄의 원칙 

소프트웨어의 구성요소(컴포넌트, 클래스, 모듈, 함수등)는 확장에는 열려있어야 하고 변경에는 닫혀있어야 한다는 원칙이다. 변경의 비용은 가능한 줄이고 확장을 위한 비용은 가능한 극대화한다. 이는 요구사항에 변경이나 추가사항이 발생하더라도 기존 구성요소의 수정은 일어나지 말아야 하며 기존 구성요소를 쉽게 확장하여 재사용할 수 있도록 한다는 뜻이다.

<br>

**[적용방법]**
* 변경될 것과 변경되지 않을 것을 엄격히 구분한다.
* 변경될 것과 변경되지 않을 것을 모두 사용하는 부분을 인터페이스나 추상클래스로 지정한다.
* 구현에 의존하기보다 정의한 인터페이스나 추상클래스에 의존하도록 작성한다

<br>

**[예제코드]**

``` java
public class Airplane {
  public void fly() {
    // 비행기를 운전
  }
}

public class Bus {
  public void drive() {
    // 버스를 운전
  }
}

public class Train {
  public void operateTrain() {
    // 기차를 운전
  }
}

//...

public class Travel {
  private Airplane airplane;
  private Bus bus;
  private Train train;
  
  public void setAirplane(Airplane air) {
    this.airplane = air;
  }
  
  public void setBus(Bus bus) {
    this.bus = bus;
  }
  
  public void setTrain(Train train) {
    this.train = train;
  }
  //...
}
```

위와 같은 코드에서 Travel에 새로운 이동수단에 대한 클래스를 추가 확장하려고 하면 코드가 복잡해지고 수정해야하는 부분도 많아진다. 따라서 위 코드를 리팩토링하여 개선해보자.

<br>

```java
public abstract class Transport {
  public abstract void move();
}

public class Airplane extends Transport {
  @Override
  public void move() {
    // 비행기를 운전
  }
}

public class Bus extends Transport {
  @Override
  public void move() {
    // 버스를 운전
  }
}

public class Train extends Transport {
  @Override
  public void move() {
    // 기차를 운전
  }
}

//...

public class Travel {
  private Transport transport;
  
  public void setTransport(Transport transport) {
    this.transport = transport;
  }
  //...
}
```

위와 같이 리팩토링을 한다면 새로운 이동수단이 추가되어 기능이 확장되더라도 Transport를 상속받아 구현한다면 Travel 코드는 변하지 않을 것이다. 그리고 이렇게 변경에 닫혀있고 확장에 유연하도록 하는 것이 OCP원칙이다.

<br>

### LSP (The Liskov Subtitution Principle)

>  리스코프 치환 원칙

LSP란 상위 클래스가 있고 상위 클래스를 상속받은 하위 클래스가 있을 때 상위 클래스는 항상 하위 클래스를 호환할 수 있어야 한다는 원칙이다. 자바에서의 다형성 개념과 관련이 있다. 대표적인 LSP의 예로 JAVA의 Collection 모듈이 있다.

<br>

**[예제 코드]**
```java
public interface Shape {
  void setWidth(int width);
  void setHeight(int height);
  double getArea();
}
```
부모 클래스(인터페이스) 인 Shape가 있다.

<br>
```java
public class Rectangle implements Shape {
  private int width;
  private int height;

  public void setWidth(int width) {
      this.width = width;
  }

  public void setHeight(int height) {
      this.height = height;
  }

  public double getArea() {
      return width * height;
  }
}

public class Triangle implements Shape {
  private int width;
  private int height;

  public void setWidth(int width) {
      this.width = width;
  }

  public void setHeight(int height) {
      this.height = height;
  }

  public double getArea() {
      return ((double) width * height) / 2;
  }
}
```
Shape를 구현한 Rectangle과 Triangle 클래스가 있다.

<br>

```java
public class Driver {
  public static void main(String[] args) {
  Shape shape;

  shape = new Rectangle();
  shape.setWidth(100);
  shape.setHeight(100);
  System.out.println(shape.getArea());

  shape = new Triangle();
  shape.setWidth(100);
  shape.setHeight(100);
  System.out.println(shape.getArea());
  }
}
```
Rectangle과 Triangle은 항상 Shape형 변수에 담을 수 있으며 Shape과 항상 호환될 수 있다. 따라서 위의 코드는 LSP원칙을 지키는 코드가 된다.

<br>

### ISP (Interface Segregation Principle)

>  인터페이스 분리의 원칙

ISP 원칙은 자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다는 것이다. 즉, 어떤 클래스가 다른 클래스에 종속될 때 가능한 최소한의 인터페이스만 사용해야한다. 범용적인 1개의 인터페이스보다 전용 인터페이스 여러개가 더 좋다라고도 정의할 수 있다.

<br>

**[예제코드]**

```java
public interface Bird {
    public void fly();
    public void sing();
    public void eat();
}

public class Pigeon implements Bird {
    @Override
    public void fly() {
        System.out.println("fly!");
    }

    @Override
    public void sing() {
        System.out.println("coo coo coo coo");
    }

    @Override
    public void eat() {
        System.out.println("eat eat");
    }
}

public class Penguin implements Bird {
    @Override
    public void fly() {
        // pass
    }

    @Override
    public void sing() {
        System.out.println("penguin penguin");
    }

    @Override
    public void eat() {
        System.out.println("eat eat");
    }
}
```

위와 같이 Bird 인터페이스가 있고 이를 상속받아 Pigeon과 Penguin 클래스를 각각 구현했다. 위 코드를 보면 Penguin 클래스에 펭귄은 날지 못하기 때문에 fly 메소드는 빈 메소드를 구현했다. 이 경우가 ISP를 위반했다고 할 수 있다. 위 코드를 ISP 원칙에 따라 리팩토링해보자.

<br>

```java
public interface Animal {
  public void sing();
  public void eat();
}

public interface FlyingAnimal {
  public void fly();
}

public class Pigeon implements Animal, FlyingAnimal {
    @Override
    public void fly() {
        System.out.println("fly!");
    }

    @Override
    public void sing() {
        System.out.println("coo coo coo coo");
    }

    @Override
    public void eat() {
        System.out.println("eat eat");
    }
}

public class Penguin implements Animal {
    @Override
    public void sing() {
        System.out.println("penguin penguin");
    }

    @Override
    public void eat() {
        System.out.println("eat eat");
    }
}
```

공통적으로 구현하는 메소드들을 하나의 인터페이스로 만들고, 일부 클래스만 구현하는 메소드를 다른 인터페이스로 분리한다. 따라서 Animal이라는 인터페이스와 FlyingAnimal 인터페이스로 분리했다. 이후 비둘기는 두 인터페이스를 모두 구현하고, 펭귄은 Animal 인터페이스만 구현하면 ISP원칙을 지킬 수 있다.

<br>

ISP 역시 SRP와 마찬가지로 코드를 분리할 것을 요구한다. 다만 무턱대로 분리하다보면 인터페이스의 갯수가 많아지고, 한 클래스가 상속받는 인터페이스가 많아지며, 도리어 구조가 복잡해진다. 그러므로 클래스 설계 초기에 최소한의 책임을 가지도록 설계하는 것이 중요하다.

<br>

### DIP (Dependency Inversion Principle)

>  의존성 역전의 원칙

의존성 역전이란 구조적 디자인에서 발생하던 하위 레벨 모듈의 변경이 상위 레벨 모듈의 변경을 요구하는 위계관계를 끊는 의미의 역전을 말한다. 쉽게 설명하면 하위 클래스의 변경이 상위 클래스의 변경에 영향을 미치지 않도록 하는 원칙이다. 실제 사용 관계는 바뀌지 않으며, 추상을 매개로 메시지를 주고 받음으로써 관계를 최대한 느슨하게 만드는 원칙이다.

<br>

**[생각 볼 문제]**

DIP의 원칙을 지킨다면 반드시 인터페이스를 이용하여 관계를 끊어야 하는가에 대한 의문이 든다. 현재 하나의 클래스만 존재하는 모듈에서 추후 확장을 위해 불필요한 인터페이스를 추가할 필요가 있는가?

이 부분은 사람마다 의견의 차이가 있을 수 있다. 추후 확장성을 위해 미리 만들어야 한다는 의견도 있고 현재 기능에 충실하게 만들고 인터페이스의 추가는 확장이 필요할 때 추가하는게 맞다는 의견도 있다. 이 부분은 해당 조직, 상황 등에 따라 설계하면 된다.

하지만 꼭 필요한 경우도 있다. 그 예로 Logger를 들 수 있다. 로그는 파일, DB, 또는 다른 웹서버 등에 남길 수가 있는데 로거는 어디에 남기는지 알 필요가 없다. 이런 경우 Logger가 로그를 남길 때 어디에 로그를 남길 지 인터페이스로 분리하여 Logger와의 관계를 분리시킬 필요가 있다. 어디에 남길지는 해당 인터페이스를 구현한 측에서 결정하고 Logger는 로깅하는 것에만 집중할 수 있도록 할 수 있다.

<br>

**[예제코드]**

```java
public interface ConnectionMaker {
    Connection makeConnection();
}

public class DConnectionMaker implements ConnectionMaker {
    public Connection makeConnection() {
        // D사를 위한 컨넥션 생성 후 반환
    }
}

public class NConnectionMaker implements ConnectionMaker {
    public Connection makeConnection() {
        // N사를 위한 컨넥션 생성 후 반환
    }
}

public class UserDao {
    private ConnectionMaker connectionMaker;

    public UserDao(ConnectionMaker connectionMaker) {
        this.connectionMaker = connectionMaker;
    }

    public void addUser(User user) {
        Connection connection = connectionMaker.makeConnection();
        // user 정보를 DB에 추가하는 로직 수행
    }
}

public class Driver {
    public static void main(String[] args) {
    	// N사와 D사의 유저 정보를 DB에 추가하는 로직은 같고, 사용하는 컨넥션만 다르다.
    	// 추상화된 컨넥션 인터페이스를 입력받을 수 있게 함으로써 위계 관계를 끊고,
    	// 이를 구현한 서로 다른 모듈을 입력받아 사용함으로써 서로 다른 컨넥션을 사용할 수 있게 함.
        UserDao nUserDao = new UserDao(new NConnectionMaker());
        nUserDao.addUser(new User("email1234", "pw1234"));

        UserDao dUserDao = new UserDao(new DConnectionMaker());
        dUserDao.addUser(new User("email1234", "pw1234"));
    }
}
```

위 코드를 보면 상위 클래스인 ConnectionMaker 인터페이스와 이를 구현한 각 N사, D사의 ConnectionMaker 클래스가 있다. 중간에 ConnectionMaker라는 인터페이스를 매개로 상위, 하위 클래스의 관계를 느슨하게 할 수 있다. 따라서 해당 모듈을 사용하는 UserDAO에서 ConnectionMaker를 이용하여 그 하위 클래스와의 관계를 끊을 수 있다.

<br>

<br>

## 전략 패턴 (Strategy Pattern)

>  어플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분으로부터 분리 시킨다.

전략 패턴은 해당 클래스의 기능에서 필요에 따라 변경이 필요한 비지니스 로직을 인터페이스를 통해 통째로 외부로 분리시키고, 이 비지니스 로직을 구현한 구체적인 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴이다.

<br>

**[예제코드]**

하나의 ConnectionMaker가 있고 각 N사, K사 별로 ConnectionMaker가 필요하다고 하자.

UserDAOTest는 전략패턴의 클라이언트이다. 컨텍스트(UserDAO)를 사용하는 클라이언트(UserDAOTest)는 컨텍스트가 사용할 전략(ConnectionMaker를 구현한 클래스)을 컨텍스트의 생성자 등을 통해 제공하고 컨텍스트의 기능을 사용한다.

<br>

**ConnectionMaker 인터페이스와 관련 클래스**

```java
public interface ConnectionMaker {
	Connection makeConnection() throws ClassNotFoundException, SQLException;
}

public class NConnectionMaker implements ConnectionMaker {
	@Override
	public Connection makeConnection() throws ClassNotFoundException, SQLException {
      	Connection connection = new Connection();
 		// N사의 독자적인 방법으로 Connection을 생성하는 코드
  		return connection;
 	}
}

public class KConnectionMaker implements ConnectionMaker {
	@Override
	public Connection makeConnection() throws ClassNotFoundException, SQLException {
        Connection connection = new Connection();
  		// K사의 독자적인 방법으로 Connection을 생성하는 코드
  		return connection;
 	}
}
```

<br>

위의 예에서 회사 별로 DB가 다르기 때문에 그에 따라 ConnectionMaker를 각각 다른 클래스를 사용해야 한다. 따라서 Connection을 리턴하는 makeConnection이라는 메서드를 정의한 ConnectionMaker 인터페이스를 정의하고 이를 상속받는 클래스들이 해당 기능을 구현하도록 했다.

<br>

**UserDAO **

```java
public class UserDAO {
	// 제공받은 전략
	private ConnectionMaker connectionMaker;

	public UserDAO(ConnectionMaker connectionMaker) {
		// 파라미터로 제공받은 오브젝트가 어떤 클래스로부터 만들어졌는지 신경쓰지 않는다.
		// Client가 ConnectionMaker를 다른 걸 주더라도 UserDao는 변함 없다.
		this.connectionMaker = connectionMaker;
	}

	// 전략패턴에서 말하는 자신의 기능(context)
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection connection = connectionMaker.makeConnection();

		//... DB에 insert하는 코드
	}

	// 전략패턴에서 말하는 자신의 기능(context)
	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection connection = connectionMaker.makeConnection();

		//... DB에서 User정보 가져오는 코드
	}
}
```

UserDAO의 add메서드와 get 메서드는 전략 패턴의 Context에 해당한다.
Context는 클래스 자신의 핵심 기능을 말하고 전략(Strategy)은 클래스가 이를 수행하는데 필요한 기능 중 변경이 발생 가능한 기능을 말한다.

UserDAO는 데이터를 조회해서 제공하는 기능이 Context에 해당한다고 볼 수 있다. 따라서 그 외의 어떤 DB에 접속하는지 등의 기능은 UserDAO에게는 중요하지 않다. 즉, 어떤 ConnectionMaker를 사용하는지 (N사 혹은 K사) UserDAO는 관심이 없다. 어떤 회사든지 접속해서 데이터를 조회하는 기능을 제공할 뿐이다.

<br>

**UserDAOTest 클래스**

```java
public class UserDAOTest {
   	// 전략 패턴의 Client
   	// UserDao가 사용할 ConnectionMaker 구현클래스를 결정하고 UserDao에게 제공하는 역할
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
     // case 1. UserDao에서 사용할 ConnectionMaker 타입의 오브젝트(전략) 생성
     ConnectionMaker connectionMaker = new NConnectionMaker();

     // case 2. K사의 ConnectionMaker를 사용할 경우 K사의 ConnectionMaker 타입의 오브젝트 생성하면 된다.
     ConnectionMaker connectionMaker = new KConnectionMaker();

     // UserDao에서 사용할 전략 제공
     // 결국 두 오브젝트 사이의 의존관계 설정 효과
     UserDAO userDAO = new UserDAO(connectionMaker);

     //... userDao를 이용한 코드
   }
}
```

따라서 UserDAO를 사용하는 UserDAOTest 클래스는 어떤 ConnectionMaker를 사용할지 UserDAO에게 전달해줄 필요가 있다. 위 코드를 보면 UserDAO를 생성할 때 connectionMaker를 선택해서 넘겨주면 그에 따라 UserDAO가 전략에 맞는 connection을 생성하여 사용할 것이다.

<br>
