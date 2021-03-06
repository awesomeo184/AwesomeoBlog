---
title: '[토비의스프링] Ch.1-(1) 관심사 분리, 제어의 역전'
date: 2021-06-30 19:44:63
category: Spring
thumbnail: { thumbnailSrc }
draft: false
---

> 본 포스팅은 <토비의 스프링 3.1>을 읽고 공부하면서 이해한 내용을 정리한 것입니다.
> 정확하고 자세한 내용은 반드시 원문 서적에서 확인바랍니다.


# 관심사 분리

회원 정보를 관리하는 DAO를 만든다고 해보자.

> **DAO(Data Access Object)**: DB를 사용해 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 오브젝트를 일컫는다.

> **자바빈(JavaBean)**: 원래 비주얼 툴에서 조작 가능한 컴포넌트를 지칭하는 용어였으나 지금은 다음의 두 관례를 따르는 오브젝트를 가리킨다.
> 
> 1. 디폴트 생성자를 갖는다. 툴이나 프레임워크에서 리플렉션을 이용해 오브젝트를 생성하는데 필요하기 때문.
> 2. 프로퍼티를 갖는다. 자바빈이 노출하는 이름을 가진 속성을 프로퍼티라 한다. > 흔히 말하는 게터세터(Getter & Setter) 

유저의 데이터를 DB에 저장하는 메서드와, DB에서 유저 정보를 조회하는 메서드를 다음과 같이 작성했다.

```java
public void add(User user) throws SQLException{
    Connection con = DriverManager.getConnection(url,username,password);
        // 회원 정보 저장 로직
        // ... 
}

public User get(String id) throws SQLException{
    Connection con=DriverManager.getConnection(url,username,password);
        // 회원 정보 조회 로직
        // ...
}
```

## 분리

객체지향 패러다임에서 가장 중요하게 여기는 것은 **변화에 대한 대응**이다. 변경 사항이 생길 때, 최소한의 코드 수정으로 변화에 대응하기 위해서는 **분리와 확장**을 고려한 설계가 필요하다.

변경에 대한 요청은 한가지 관심사에 대해서만 일어난다. 따라서 하나의 관심사는 한 구역에 몰아 넣어야한다.
그래야 변경 요청이 있을 때 여러군데를 손봐야하는 일이 생기지 않는다. DB 접속용 암호를 변경하라는 요청이 들어왔는데, 클래스를 수백개 고쳐야하는 상황이 발생해서는 안되지 않는가.

관심이 같은 것끼리는 모으고, 관심이 다른 것은 떨어뜨려놓는 **관심사의 분리(Separation of Concerns)**를 잘 지켜야 한다.

관심사의 분리를 위해 UserDao의 관심 사항을 분석해보면 다음과 같다.

1. DB 연결을 위한 커넥션을 어떻게 가져올 것인가. 더 세분화하면 어떤 DB를 사용할 것이고, 어떤 로그인 정보를 쓰는지까지도 나눌 수 있다.
2. 사용자 등록을 위해 SQL 문장을 담을 Statement를 만들고 실행한다.
3. 작업이 끝나고 리소스를 반환한다.

<br>

### 방법1 - 메서드 추출

가장 간단한 방법은 중복되고 있는 코드를 추출하는 것이다. 현재 add(), get() 메서드에서 DB 커넥션을 가져오는 부분이 중복되어 나타나므로 다음과 같이
리팩토링 할 수 있다.

```java
public void add(User user) throws SQLException{
    Connection con = DriverManager.getConnection(url,username,password);
        //... 
}

public User get(String id) throws SQLException{
    Connection con = DriverManager.getConnection(url,username,password);
        //...
}
```

↓

```java{2,7}
public void add(User user) throws SQLException{
    Connection con = getConnection();
        ...
}

public User get(String id) throws SQLException{
    Connection con = getConnection();
        ...
}

private Connection getConnection() throw SQLException{
    return DriverManager.getConnection(url,username,password);
}
```

<br />

### 방법 2 - DB 커넥션 만들기의 독립 (상속을 통한 확장)

우리의 DAO가 발전을 거듭해서 N사와 D사에서 우리의 DAO를 구매하겠다는 연락이 왔다. 문제는 N사와 D사가 각기 다른 종류의 DB를 사용하고 있고 DB 커넥션을 만드는데
있어서 각기 다른 방법을 사용하고 싶다는 것이다. 그리고 UserDao를 구매한 이후에도 DB 커넥션 방법이 종종 변화할 수 있다고 한다.

이런 경우 UserDao의 소스코드를 공개하지 않고도 고객 스스로 원하는 DB 커넥션을 만들어 사용할 수 있는 방법은 무엇일까?

UserDao를 추상클래스로 만들고 getConnection 메서드를 추상메서드로 만든 후, N사와 D사는 UserDao를 상속받은 Dao를 사용하게 하면 된다.

```java
public abstract class UserDao {

    public void add(User user) throws SQLException {
        Connection con = getConnection();
        ...
    }

    public User get(String id) throws SQLException {
        Connection con = getConnection();
        ...
    }

    public abstract Connection getConnection() throws SQLException;

}
```

N사의 Dao

```java
public class NUserDao extends UserDao {

    private final String url = "";
    private final String username = "";
    private final String password = "";

    @Override
    public Connection getConnection() throws SQLException {
        // N사의 독자적인 DB 커넥션 코드
        return DriverManager.getConnection(url, username, password);
    }
}
```

이렇게 슈퍼클래스에 기본적인 로직의 흐름(커넥션 가져오기, SQL 생성, 실행, 반환)을 만들고, 그 기능의 일부를 추상 메서드나 오버라이딩이 가능한 protected 메서드
등으로 만든 뒤 서브클래스에서 이런 메서드를 필요에 맞게 구현하여 사용하도록 하는 방법을 디자인 패턴에서 **Template Method Pattern**이라고 한다.

UserDao의 getConnection 메서드는 Connection 타입의 오브젝트를 생성한다는 기능을 정의해놓은 추상 메서드이다. NUserDao의 getConnection
메서드는 어떤 Connection 클래스의 오브젝트를 어떻게 생성할 것인지를 결정하는 방법이라고도 볼 수 있다. 이렇게 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는
것을 **Factory Method Pattern**이라고 한다.

UserDao는 Connection 인터페이스 타입의 오브젝트를 반환하는 것 외에는 관심이 없다. UserDao는 어떤 기능을 사용한다는 데에만 관심이 있고, NUserDao나
DUserDao는 어떤 식으로 Connection 기능을 제공하는지에 관심을 두고 있는 것이다. 또, 어떤 방법으로 Connection 오브젝트를 만들어내는지도 NUserDao와
DUserDao의 관심사항이다.

> **템플릿 메서드 패턴**
>
> 상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 가장 대표적인 방법이다. 변하지 않는 기능은 슈퍼클래스에 만들어두고 자주 변경되며 확장할 기능은 서브클래스에서 만들도록 한다.
> 슈퍼클래스에서는 미리 추상 메서드 또는 오버라이드 가능한 메서드를 정의해두고 이를 활용해 코드의 기본 알고리즘을 담고 있는 템플릿 메서드를 만든다. 
> 슈퍼클래스에서 디폴트 기능을 정의해두거나 비워뒀다가 서브클래스에서 선택적으로 오버라이드할 수 있도록 만들어둔 메서드를 훅(hook) 메서드라고 한다.

> **팩토리 메서드 패턴**
> 
> 슈퍼클래스의 코드에서는 서브클래스에서 구현할 메서드를 호출해서 필요한 타입의 오브젝트를 가져와 사용한다. 
> 이 메서드는 주로 인터페이스 타입으로 오브젝트를 리턴하므로 서브클래스에서 구체적으로 어떤 오브젝트를 리턴하는지는 알 수 없다. 
> 그냥 해당 타입의 인터페이스에 정의된 메서드를 사용하는 것이다. 이렇게 서브클래스에서 오브젝트 생성 방법과 클래스를 결정할 수 있도록 미리 정의해둔 메서드를 팩터리 메서드라고 하고, 
> 이 방식을 통해 오브젝트 생성 방법을 나머지 로직, 즉 슈퍼클래스의 기본 코드에서 독립시키는 방법을 팩토리 메서드 패턴이라고 한다.

![image](https://user-images.githubusercontent.com/63030569/123921162-ad0a9700-d9c1-11eb-9ad7-b607297bdb32.png)


## 확장

위의 방법은 상속을 사용했다는 단점이 있다. 상속을 통한 분리에는 다음과 같은 문제점이 있다.

1. 자바는 다중상속이 안되기 때문에 NUserDao가 다른 클래스를 반드시 상속해야 한다면 UserDao를 상속할 수 없다.
2. 슈퍼클래스의 내부에 변경이 있을 때, 모든 서브클래스를 함께 수정하거나 다시 개발해야 할 수 있다.
3. 확장된 기능인 DB 커넥션을 생성하는 코드를 다른 DAO에서 사용할 수 없다. (UserDao만 가능)

두 개의 관심사인 DB 연결과 DB 입출력을 아얘 다른 클래스로 분리해보자

```java
public interface SimpleConnectionMaker {

    Connection makeConnection() throws SQLException;
}
```

```java
public class DConnectionMaker implements SimpleConnectionMaker {

    private final String url = "jdbc:mysql://localhost:3306/spring";
    private final String username = "daum";
    private final String password = "****";

    @Override
    public Connection makeConnection() throws SQLException {
        // D사의 독자적인 ConnectionMaker
        return DriverManager.getConnection(url, username, password);
    }
}
```

그리고 DUserDao를 제거하고 추상클래스이던 UserDao를 구체 클래스로 만든 후, ConnectionMaker를 의존하도록 만든다.

```java{6}
public class UserDao {

    private SimpleConnectionMaker simpleConnectionMaker;

    public UserDao() {
        simpleConnectionMaker = new DConnectionMaker();
    }

    public void add(User user) throws SQLException {
        Connection con = simpleConnectionMaker.makeConnection();
    ...
    }

    public User get(String id) {
        Connection con = simpleConnectionMaker.makeConnection();
    ...
    }
}
```

이제 UserDao를 D사에 팔아도 D사는 DB 접속용 클래스를 각자 만들어서 사용할 수 있다. 그러나 UserDao가 DConnectionMaker라는 구체적인 클래스에 의존하고
있다.

```simpleConnectionMaker = new DConnectionMaker();```

이 코드 한 줄은 간결하지만 충분히 독립적인 관심사를 담고 있다. **UserDao가 어떤 ConnectionMaker 객체를 사용할 것인지를 결정한다.** 이 관심사를 담은 코드를
UserDao에서 분리하지 않으면 UserDao는 결코 독립적으로 확장 가능한 클래스가 될 수 없다.

그런데, UserDao가 어떤 구체 클래스를 사용할 것인가를 UserDao 외부에서 결정할 수 있을까?

UserDao의 클라이언트에서 메서드 파라미터나 생성자 파라미터를 통해 오브젝트를 전달해주면된다. 현재 main 메서드가 UserDao의 클라이언트이다. UserDao가 생성자를
통해서 ConnectionMaker 객체를 전달받도록 코드를 수정한 뒤 메인 메서드에서 구체적인 객체를 전달해보자.


```java
public class UserDao {

    private SimpleConnectionMaker simpleConnectionMaker;

    public UserDao(SimpleConnectionMaker simpleConnectionMaker) {
        this.simpleConnectionMaker = simpleConnectionMaker;
    }
  ...
}
```

```java
public class UserDaoTest {

    public static void main(String[] args) throws SQLException {
        DConnectionMaker connectionMaker = new DConnectionMaker();
        UserDao dao = new UserDao(connectionMaker);
    ...
    }
}
```

이렇게하면 UserDao는 자신이 어떤 구체 클래스를 사용하는지 컴파일 타임에서 알지 못한다. UserDao가 사용할 구체 클래스가 결정되는 시점, 즉 의존관계가 형성되는 시점은
런타임이다. 이렇게 외부에서 ConnectionMaker를 주입해줌으로써 컴파일타임에 UserDao와 DConnectionMaker 사이의 의존관계가 사라졌다.

![image](https://user-images.githubusercontent.com/63030569/123923746-3b801800-d9c4-11eb-8e22-287e018044fe.png)

<br>

이렇게 **인터페이스를 도입해서 컴파일 타임의 의존성을 런타임 의존성으로 교체해주면 결합도가 낮아진다.**

<br>

> **높은 응집도와 낮은 결합도**
>
> 응집도가 높다는 것은 하나의 관심사가 하나의 모듈에 집중되어 있다는 것이다. 응집도가 높으면 변경사항이 일어나도 몇 개의 모듈만 수정하면 된다.
> 응집도가 낮을수록 하나의 관심사가 여러 모듈에 퍼져있기 때문에 하나의 변경사항을 적용하기 위해 수많은 모듈을 수정해야 한다. 이는 당연히 버그의 발생률을 높인다.
>
> 결합도는 의존성과 관련이 있는데, 결합도란 '하나의 오브젝트가 변경이 일어날 때에 관계를 맺고 있는 다른 오브젝트에게 변화를 요구하는 정도'라고 말할 수 있다.
> 결합도가 높을수록 변경에 따르는 작업량이 많아지고 당연히 버그의 발생률도 높아진다.
> 결합도를 낮추기 위해서는 모듈 간의 연결을 느슨하게 유지해야 하는데, 인터페이스로 모듈을 연결해주면 결합이 느슨해진다.
> 예시에서 UserDao와 ConnectionMaker의 관계를 보면, UserDao는 ConnectionMaker의 구현 클래스도 모르고, 구현 방법이나 전략, 그것이 사용하는 오브젝트에
> 대해서도 관심이 없다. 인터페이스를 통해 결합도를 낮춰주었기 때문이다.

> **전략패턴**
>
> 개선한 UserDaoTest-UserDao-ConnectionMaker 구조를 디자인 패턴의 시각으로 보면 **전략 패턴**이라고 볼 수 있다.
> 전략 패턴은 자신의 기능 맥락에서, 필요에 따라 변경이 필요한 알고리즘을 인터페이스를 통해 통째로 외부로 분리시키고, 이를 구현한
> 구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴이다.
> 예시의 UserDaoTest에서 ConnectionMaker를 필요에 따라 NConnectionMaker, DConnectionMaker로 바꿔가면서
> UserDao에게 전달할 수 있는 것처럼 말이다.

<br>

# 제어의 역전(IoC)

지금까지 UserDao를 리팩토링하면서 신경쓰지 않은 것이 한 가지 있는데, 바로 UserDaoTest이다. UserDaoTest는 지금까지 UserDao가 담당하던 기능, 즉
어떤 ConnectionMaker의 구현클래스를 사용할지 담당하던 역할을 엉겁결에 떠맡았다.

원래 UserDaoTest는 UserDao가 잘 동작하는지 확인하기 위해 만든 것인데 다른 책임을 떠안게 되었다. 그러므로 이 기능도 독립적으로 분리해보자.

## 팩토리

객체의 생성 방법을 결정하고 그렇게 만들어진 오브젝트를 돌려주는 역할을 하는 오브젝트를 흔히 **팩토리**라고 부른다. 이는 디자인 패턴에서 말하는 특별한 문제를 해결하기 위해
사용되는 추상 팩토리 패턴이나, 팩토리 메서드 패턴과는 다르니 혼동하지 말자. 단지 오브젝트를 생성하는 쪽과 생성된 오브젝트를 사용하는 쪽의 역할과 책임을 깔끔하게 분리하려는
목적으로 사용하는 것이다.

팩토리 역할을 맡을 클래스를 DaoFactory라고 하자.

```java
public class DaoFactory {

    public UserDao userDao() {
        SimpleConnectionMaker connectionMaker = new DConnectionMaker();
        return new UserDao(connectionMaker);
    }
}
```

```java
public class UserDaoTest {

    public static void main(String[] args) throws SQLException {
        UserDao dao = new DaoFactory().userDao();
        ...
    }
}
```

UserDao와 ConncetionMaker는 각각 애플리케이션의 데이터 로직과 기술 로직을 담당하고, DaoFactory는 이런 애플리케이션의 오브젝트들을 구성하고 그 관계를
정의하는 책임을 맡고 있다. DaoFactory는 애플리케이션을 구성하는 컴포넌트들의 관계를 정의한 설계도라고 볼 수 있다.

이제 N사와 D사에 UserDao를 공급할 때, UserDao, ConnectionMaker와 함께 DaoFactory도 함께 제공한다. UserDao와 달리 DaoFactory는
소스코드를 제공한다. 새로운 관계가 필요하면 DaoFactory를 직접 수정한다.

DaoFactory를 분리했을 때 얻을 수 있는 장점은 매우 다양하다. 그 중에서도 컴포넌트를 담당하는 오브젝트와 애플리케이션의 구조를 결정하는 오브젝트를 분리했다는 데 가장
의미가 있다.


![image](https://user-images.githubusercontent.com/63030569/123925807-3a4fea80-d9c6-11eb-9633-fe446cb458a3.png)


## 제어의 역전 (Inversion of Control)

이제 **제어의 역전**이라는 개념에 대해 알아보자. 제어의 역전이란 프로그램의 제어 흐름이 뒤바뀐다는 것이다.

일반적인 프로그램의 제어 흐름은 1)프로그램의 시작 지점(일반적으로 main 메서드)에서 다음에 사용할 오브젝트를 결정하고, 2)결정한 오브젝트를 생성하고 3)오브젝트에 있는
메서드를 호출하고 4)그 오브젝트 메서드 안에서 다음에 사용할 것을 결정하고 호출하는 식의 작업이 반복된다.

이런 흐름에서 각 오브젝트는 자신이 사용할 오브젝트를 직접 생성한다. UserDao가 자신이 사용할 ConnectionMaker를 직접 생성했던 것처럼 말이다.

제어의 역전이란 이런 제어 흐름의 개념을 거꾸로 뒤집는 것이다. **제어의 역전에서는 오브젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않는다.** 모든 제어의 권한은 자신이 아닌 다른
대상에게 위임한다. 예시에서는 DaoFactory가 모든 제어권을 가지고 있다.

프레임워크와 라이브러리를 구분짓는 핵심이 제어의 역전이다. 라이브러리는 프로그래머가 실행 흐름을 설계하고 필요한 코드를 라이브러리에서 가져다 쓴다. 프레임워크는 프로그래머가 작성한
코드를 프레임워크의 실행 흐름안에서 프레임워크가 가져다 쓴다.

우리가 관심을 분리하고 책임을 나누고 유연하게 확장 가능한 구조로 만들기 위해 DaoFactory를 도입했던 과정이 바로 IoC를 적용하는 작업이었다. IoC는 특별한게 아니다.
**객체 지향의 원칙을 지키면서 설계를 할 때, 자연스럽게 IoC 구조를 갖추게된다.**

제어의 역전에서는 프레임워크 또는 컨테이너와 같이 애플리케이션 컴포넌트의 생성과 관계설정, 시용, 생명주기 관리 등을 관장하는 존재가 필요하다. DaoFactory는 오브젝트
수준의 가장 단순한 IoC 컨테이너 내지는 IoC 프레임워크라고 불릴 수 있다. **IoC를 애플리케이션 전반에 걸쳐 본격적으로 적용하려면 스프링의 도움을 받는 것이 훨씬 유리하다.**

<br>

다음 포스팅에서 지금까지 리팩토링을 통해 IoC를 적용한 것과 스프링을 이용하는 것이 어떤 차이가 있는지 정리하도록 하겠다.
