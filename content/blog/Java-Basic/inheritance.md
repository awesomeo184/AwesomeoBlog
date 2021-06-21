---
title: '[Java Study 6주차] 상속'
date: 2021-01-08 15:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

* 목표: 자바의 상속에 대해 학습하세요.

공부할 것

* 자바 상속의 특징
* super 키워드
* 메소드 오버라이딩
* 다이나믹 메서드 디스패치 (Dynamic Method Dispatch)
* 추상 클래스
* final 키워드
* Object 클래스

# 자바 상속의 특징

## 상속이란?

상속은 기존의 클래스를 재사용하여 새로운 클래스를 작성하는 것이다. 자바에서 상속을 구현하려면 class 선언부에 extends 키워드를 사용하면 된다.

```java
class 자식클래스 extends 부모클래스 {}
이 두 클래스는 서로 상속 관계에 있다고 표현하며, 상속해주는 클래스를 '조상 클래스', 상속 받는 클래스를 '자손 클래스'라고 한다. 이외에도 다양한 이름으로 불린다.
```

```
조상 클래스 = 부모(parent) 클래스 = 상위(super) 클래스 = 기반(base) 클래스

자손 클래스 = 자식(child) 클래스 = 하위(sub) 클래스 = 파생된(derived) 클래스
```

상속 관계에 있는 클래스는 상속 계층도에서 화살표로 나타낸다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJTeE6%2FbtqTcQ4wlE6%2FslTzv68ZskezkH03uourM1%2Fimg.png)

<br>

자손 클래스는 조상 클래스의 모든 멤버를 상속받는다. 거기에 자손 클래스만 갖는 필드를 추가로 정의할 수 있기 때문에 자손 클래스의 멤버의 수는 항상 조상 클래스의 멤버의 수보다 크거나 같다. 그래서 두 클래스의 다이어그램을 다음과 같이 표현할 수도 있다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeHgtCg%2FbtqSZxTnexQ%2FpIDfQH9bSjHGmRuaWqQg2K%2Fimg.png)

자손 클래스는 부모 클래스의 멤버를 모두 상속받고, 부모 클래스의 변경 사항 또한 그대로 반영되기 때문에 상속을 통해서 클래스를 관리하면 코드의 양을 줄이고 신뢰도를 높일 수 있다.

## 포함관계

클래스를 재사용하는 방법에는 상속 이외에도 **포함(Composite)관계**를 맺어주는 방법이 있다. 포함 관계를 맺는다는 것은 한 클래스의 맴버 변수로 다른 클래스 타입의 참조 변수를 선언해주는 것을 말한다.

예를 들어, 중심점과 반지름을 멤버 변수로 같는 Circle 클래스는 다음과 같이 정의될 수 있다.

```java
class Circle {
    int x;    // 중심점의 x 좌표
    int y;    // 중심점의 y 좌표
    int r;    // 원의 반지름
}
```

<br>

여기서 x, y 좌표를 멤버로 갖는 Point 클래스를 선언해서 다음과 같이 포함관계를 맺어줄 수 있다.

```java
class Circle {
    Pointer center = new Point();
    int r;
}

class Point {
    int x;
    int y;
}
```

<br>

이렇게 클래스를 분리해주면 다른 도형, 예를 들어 타원, 삼각형 같은 클래스를 만들때 Point 클래스를 재사용 할 수 있고, Point와 관련된 로직과 도형과 관련된 로직을 분리해서 관리할 수 있다.

코드를 작성하다보면 포함관계를 맺어줘야 할지, 상속관계를 맺어줘야 할지 헷갈리는 때가 있는데 그 때는 `has a`, `is a`로 문장을 만들어 보면 좀 더 쉽게 구분을 할 수 있다.

예를 들어, 포유류와 인간은 포함 관계가 아니라 상속 관계이다. 인간은 포유류가 갖고 있는 특징을 모두 가지고 있으면서 추가로, 이족 보행을 한다. 언어 능력을 갖추고 있다 등의 특징을 더 가지고 있기 때문이다. 인간과 포유류의 관계는 다음과 같이 표현할 수 있다.

```
상속관계 : 인간은 포유류다.(인간 is a 포유류)
원과 점의 관계는 어떠한가. 점은 원을 이루는 일부일 뿐이므로 포함관계이다. 원과 점의 관계는 다음과 같이 표현할 수 있다.
```

```
포함관계 : 원은 점을 가지고 있다. (원 has a 점)
인간은 포유류를 가지고 있다, 원은 점이다 혹은 그 반대는 논리적으로 어색하기 때문에 has a, is a 로 문장을 만들어 보면 둘 간의 관계를 더욱 쉽게 파악할 수 있다.
```

## 단일 상속

C++ 이나 파이썬같은 언어들은 여러 조상 클래스로부터 상속을 받는 다중 상속이 가능하다. 그러나 Java의 경우 단일 상속만 허용한다. 다중 상속을 허용하면 좀 더 복합적인 기능을 가진 클래스를 쉽게 작성할 수 있다는 장점이 있지만 몇가지 단점이 있는데,
그 중 하나가 두 클래스를 상속 받을 때 조상 클래스 사이에 중복되는 멤버의 이름이 존재할 때 이를 구분하기가 어렵다는 것이다. 이를 해결하려면, 조상 클래스 멤버의 이름을 고치든가, 상속 순서에 대한 규약을 정해야하는데, 자바는 이를 포기하고 그냥 단일 상속만 허용하는 방법을 택했다.

# super keyword

super 키워드는 this 키워드를 이해했다면 어렵지 않다. `super()`는 부모 클래스의 생성자를 호출하고, `super`는 부모 클래스의 멤버 변수에 접근하는 참조 변수이다.

```java
class Parent {
    String name;
    int age;
}

public class Child extends Parent{

}
```

Parent와 Child 모두 생성자를 정의하지 않았기 때문에 컴파일러가 자동적으로 기본 생성자를 추가한다. 그런데, 다른 클래스를 상속받는 클래스는 기본 생성자에 조상 클래스의 생성자를 호출하는 super()가 하나 더 추가된다.

```java
class Parent {
    String name;
    int age;

    Parent() {};    // 컴파일러가 자동으로 추가하는 부분
}

public class Child extends Parent{

    public Child() {    //
        super();        //
    }                   // 컴파일러가 자동으로 추가하는 부분
}
``` 

<br>

기본 생성자는 클래스에 생성자가 하나도 없을 때만 추가된다. 그래서 조상 클래스에 매개변수를 가진 생성자를 추가한다면 컴파일 에러가 발생한다.

```java
class Parent {
    String name;
    int age;

    Parent(String name, int age) {
        this.name = name;
        this.age = age;
    };
}

public class Child extends Parent{

    public Child() {
        super();    // 에러 발생 : 조상 클래스에 기본 생성자가 없음
    }
}
```

<br>

이 때는 조상 클래스에 매개변수가 없는 생성자를 추가해주거나, 자손 클래스의 super()에 인자를 전달해줘야 한다.

```java
class Parent {
    String name;
    int age;

    Parent(String name, int age) {
        this.name = name;
        this.age = age;
    }

    Parent() {
        this("Jack", 40);
    }
}

public class Child extends Parent{

    public Child() {
        // Parent() 를 정의하지 않았다면
        // super("Jack", 40);
        // 추가 해줘야 에러가 발생안함
    }
}
```

<br>

super는 조상 클래스의 멤버 변수에 접근하는 참조 변수이다. 예시를 통해 확인해보자.

```java
class Parent {
    String name;
    int age;

    Parent(String name, int age) {
        this.name = name;
        this.age = age;
    }

    Parent() {
        this("Jack", 40);
    }
}

public class Child extends Parent{
    String name;

    public Child(String name) {
        this.name = name;
    }

    void displayName() {
        System.out.println(super.name);
        System.out.println(this.name);
    }

    public static void main(String[] args) {
        Child child = new Child("James");
        child.displayName();
    }
}
```

<br>

실행 결과

```
Jack
James
```

물론 메서드에도 접근 가능하다.

## 메서드 오버라이딩(Method Overriding)

메서드 오버라이딩은 조상 클래스에서 상속받은 메서드를 자손 클래스에서 재정의하는 것을 말한다. 쉽게 말하면 메서드를 덮어쓰는 것이다.

아래 이차원 좌표를 구현한 Point2D가 있다. getLocation은 해당 좌표의 위치를 반환하는 메서드이다.

```java
public class Point2D {
    int x;
    int y;

    String getLocation() {
        return "x = " + x + ", y = " + y;
    }
}
```

<br>
<br>

시간이 지나 삼차원 좌표를 표현해야 할 일이 생겨 Point2D 클래스를 상속받는 Point3D 클래스를 만들었다.
이 때, 기존의 Point2D 클래스를 사용하던 사용자들은 Point3D에서도 좌표의 위치를 반환하려면 getLocation 메서드를 사용해야 할 것이라고 기대할 것이다. 그렇기 때문에 get3DLocaion 같은 새로운 이름의 메서드를 만드는 것보다는 getLocation을 오버라이딩 하는 것이 더 좋은 선택이다.

```java
public class Point2D {
    int x;
    int y;

    String getLocation() {
        return "x = " + x + ", y = " + y;
    }

}

class Point3D extends Point2D{
    int z;

    @Override
    String getLocation() {
        return super.getLocation() + ", z = " + z;
    }
}
```

@Override 애너테이션을 달아주면, 해당 메서드가 상위 클래스의 메서드를 잘 오버라이딩하는지 컴파일러가 체크해준다.

오버라이딩에는 다음과 같은 조건이 있다.

* 오버라이딩할 조상 메서드와 메서드 시그니처가 같아야 한다.
* 오버라이딩할 조상 메서드가 공변 반환 타입(covariant return type)이거나 리턴 타입이 같아야한다.
* 접근 제어자는 조상 클래스의 메서드보다 좁은 범위로 변경할 수 없다.
* 조상 클래스보다 많은 수의 예외를 선언할 수 없다.

<br>

메서드 시그니처는 메서드를 구분할 수 있는 기준이 되는 것을 말한다. 자바의 메서드는 (**메서드 이름, 파라미터 타입, 파라미터 개수**)로 구분된다.

공변 반환 타입이란, 메서드가 자손 클래스에서 오버라이드될 때 더 좁은 타입으로 교체될 수 있는 것을 말한다. 좁은 타입이라는 말이 잘 이해가 안됐는데 그냥 하위 타입을 말하는 것 같다. 아래는 위키에 나온 예시이다.

```java
 // Classes used as return types:
 
 class A {
 }
 
 class B extends A {
 }
 
 // "Class B is narrower than class A"
 // Classes demonstrating method overriding:
 
 class C {
     A getFoo() {
         return new A();
     }
 }
  
 class D extends C {
//Overriding getFoo() in parent class C
     B getFoo() {
         return new B();
     }
 }
```
이렇게 이해하면 될 것 같다. B가 A를 상속했다면, B는 A가 할 수 있는 모든 것을 다 할 수 있고 추가 기능을 더 가질 수 있다. 만약 다른 메서드에서 반환 타입이 A라면, 그걸 오버라이딩한 메서드는 B를 반환해도 된다. 왜냐하면 B는 A가 가진 기능들 다 가지고 있으니까.

처음에는 좁은(narrow) 타입이라는 말 때문에 이게 자손을 말하는 건지 조상을 말하는 건지 헷갈렸다. 자손은 조상의 기능 + 자신의 기능이 있으니까 더 넓은 타입 아닌가? 했는데,
조상 타입은 여러 개의 자손 타입을 가질 수 있으니 자손 타입을 좁은 타입이라고 부르는게 맞는 것 같다.

# 메서드 디스패치(Method Dispatch)

메서드 디스패치는 어떤 메서드를 실행할지 선택하고 실행하는 것을 말한다. 메서드 디스패치의 종류에는 **static, dynamic**이 있고
dynamic dispatch를 응용한 double dispatch가 있다.

## 정적 메서드 디스패치 (Static method dispatch)

어떤 메서드를 실행할지 컴파일 시점에서 결정하는 것이다. 이 경우 컴파일러가 어떤 메서드를 실행해야 하는지 알고 있다.

예를 들어

```java
class Service {

    void run() {
        System.out.println("run");
    }
}

public class Dispatch {

    public static void main(String[] args) {
        Service service = new Service();
        service.run();
    }
}
```

이 경우 service.run() 이라는 코드가 어떤 메서드를 실행하는지는 자명하다. Service라는 구체적인 클래스의 인스턴스를 가리키고 있는 참조변수의 메서드를 실행한다. 즉 컴파일 타임에서 실행될 메서드가 결정된다.

메서드 오버로딩의 경우에도 Static method dispatching이 된다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzcKM3%2FbtqS6YJnh85%2F5zIQwnr4QoMuKOdEgQzqP0%2Fimg.jpg)

이 경우에도 run의 인자로 String이 들어왔기 때문에, Service 객체가 갖고 있는 같은 이름의 메서드(run) 두 개 중, 무엇이 실행되어야 할지 컴파일 타임에 결정이 된다. 위 스크린샷은 인텔리제이에서 코드를 작성한 것인데,
Service 클래스의 메서드 두 개를 보면 String이 전달되는 run method은 글씨가 파란색이고, int가 전달되는 run method는 글씨가 회색이다. 컴파일 시점에서 이미 String을 매개변수로 받는 메서드가 실행될 것이라는 걸 알고 있다는 뜻이다. 

## 동적 메서드 디스패치 (Dynamic method dispatch)

동적 메서드 디스패치는 어떤 메서드가 실행될 지 컴파일 타임에서는 알 수 없고, 런타임에서 결정되는 것이다.

**상위 타입의 메서드를 오버라이딩할 경우 dynamic dispatching이 일어난다.**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fp8kAQ%2FbtqThZN1jvK%2FgcpAJ4mhmMAQW0Rkbl1mN0%2Fimg.jpg)

이번에는 Service라는 인터페이스를 MyService1과 MyService2가 구현했다. 그리고 메인 메서드에서 service 변수를 추상 타입으로 선언하고 MyService1 객체를 할당한 모습이다.

이때 service.run() 코드를 보고 컴파일러는 MyService1의 run 메서드를 실행할지, MyService2의 run 메서드를 실행할지 알 수 없다.
코드를 읽는 사람의 입장에서는 이것이 MyService1이라는 클래스에 정의된 메서드가 실행될 것이란 걸 알 수 있지만 컴파일러는 알지 못한다.

실제 컴파일을 해보면, 클래스 파일에서는 단지 "Service라는 타입의 run 메서드를 실행한다"라는 정보만 갖고 있지 이게 MyService1의 run 메서드인지 MyService2의 run 메서드인지에 대한 정보는 갖고 있지 않다.
어떤 메서드가 실행될지는 런타임에 동적으로 결정된다. 이게 동적 메서드 디스패치이다.

# 추상 클래스 

클래스가 객체에 대한 설계도라면, 추상 클래스는 미완성 설계도이다. 추상 클래스에는 추상 메서드를 정의할 수 있는데 선언부(반환 타입, 메서드 이름, 매개변수 타입, 수)만 정의하고 실제 구현은 추상 클래스를 상속받는 구체 클래스에서 이루어진다. 
추상 클래스와 추상 메서드는 abstrcat 키워드를 붙여서 선언한다.

**추상 클래스를 상속받는 구체 클래스는 추상 클래스에 정의된 추상 메서드를 모두 구현해야만 한다.** 하나라도 구현하지 않으면 자식 클래스 또한 추상 클래스로 지정해주어야 한다. 
그래서 추상 클래스를 정의할 때, 자식 클래스가 반드시 구현해야하는 메서드는 abstact를 붙여서 추상 메서드로 만들어주고, 구현하지 않아도 되는 메서드는 일반 메서드로 정의한다.

추상 클래스는 오로지 상속을 위해서 만들어지기 때문에 추상 클래스로는 객체를 만들 수 없다.

```java
public class AbsDemo {

    public static void main(String[] args) {
        // 공통된 특징을 가진 객체들을 추상 클래스로 관리하기 때문에 서로 다른 객체이지만 리스트로 만들 수 있음.
        List<Unit> units = Arrays.asList(new Marine(), new Tank(), new Dropship());
        
        for (Unit unit : units) {
            unit.move(100, 200);
        }
    }
}

abstract class Unit {
    int x, y;

    abstract void move(int x, int y);

    void stop() { /* 현재 위치에서 정지 */}
}

class Marine extends Unit {

    @Override
    void move(int x, int y) {/* 지정된 위치로 이동 */}

    @Override
    void stop() { /* 정지 */}

    void stimPack() {/* 스팀팩 사용 */}
}

class Tank extends Unit {

    @Override
    void move(int x, int y) {/* 지정된 위치로 이동 */}

    @Override
    void stop() { /* 정지 */}

    void changeMode() {/* 공격 모드 변경 */}
}

class Dropship extends Unit {
    @Override
    void move(int x, int y) {/* 지정된 위치로 이동 */}

    @Override
    void stop() { /* 정지 */}

    void load(Unit unit) {/* 승선 */}

    void unload(Unit unit) {/* 하선 */}
}
```

# final keyword

final은 클래스, 메서드, 필드에 사용될 수 있다. final 키워드의 핵심은 immutablilty, 즉 불변성이다.

final class는 다른 클래스가 상속할 수 없다. 확장이나 변경이 일어나서는 안되는 클래스는 final을 붙여 선언해주자.

final method는 오버라이딩 할 수 없다. 마찬가지로 확장이나 변경이 일어나서는 안되는 메서드는 final을 붙여 선언해준다.

final을 붙여서 확장이나 변경을 하지 못하도록 만드는 것은 장점이 될 수도 있지만 단점이 될 수도 있다. 클래스나 메서드에 final을 붙일 때는 왜 final이 붙어야 하는지 이유를 잘 따져본 후 반드시 그럴만한 이유가 있을 때만 붙이고, 이유를 문서에 명세하는 것이 좋다.



필드에 final을 붙이면 해당 변수를 재할당 하는 것을 막는다. 한마디로 해당 변수를 Read-only로 만든다.

클래스, 메서드와 반대로 filed를 선언할 때는 왜 final이 붙으면 안되는지를 생각해보는 것이 좋다. 필드에 final을 붙여주면 컴파일러가 안전하게 캐싱할 수 있기 때문에 속도 측면에서도 이점이 있다. 단, final이 붙은 참조 변수가 가리키는 객체의 상태는 불변이 아니다. 예를 들어 배열을 가리키는 변수에 final을 붙이면, 배열 자체를 재할당 하는 것은 막을 수 있지만, 배열의 요소를 변경하는 것은 막지 못한다.

final이 붙은 필드는 반드시 선언과 함께 초기화되거나, 생성자에서 초기화되어야 한다.

final 사용에 대한 지침은 아래의 글을 참조했다. 한번 읽어보면 좋을 듯 하다.

https://www.ibm.com/developerworks/java/library/j-jtp1029/index.html

# Object 클래스

Object 클래스는 모든 클래스의 조상이 되는 클래스이다. 그래서 자바의 모든 객체는 Object의 멤버들을 바로 사용할 수 있다.

Object 클래스는 java.lang 패키지에 포함되어 있다. java.lang 패키지는 자바의 기본이 되는 클래스들이 포함되어 있고 import문 없이도 사용 가능하다.

Object 클래스에 멤버 변수는 없으며 오직 11개의 메서드만 가지고 있다. 사용자 정의 타입을 만들다보면 Object의 메서드들을 적절히 오버라이딩 해서 사용할 일이 많으므로 잘 알아두는 것이 좋다.


## equals

equals 메서드는 객체 자신의 주소값과 인자로 들어온 객체의 주소값을 비교한다.

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

따라서 같은 값을 가지고 있더라도, 서로 다른 객체이면 eqauls의 값은 false이다. 객체는 메모리의 빈 공간에 생성되므로 서로 다른 두 객체가 같은 주소값을 가질 일은 없다. 단 서로 다른 참조 변수가 같은 주소를 가리키는 것은 가능하다. 코드로 표현하면 다음과 같다.

```java
public class ObjDemo {
    public static void main(String[] args) {
        Value val1 = new Value(10);
        Value val2 = new Value(10);

        System.out.println(val1.equals(val2));

        val1 = val2;    // val1이 val2가 참조하고 있는 객체의 주소를 가리킴

        System.out.println(val1.equals(val2));
    }
}

class Value {
    int value;
    Value(int value) {
        this.value = value;
    }
}
```

<br>

Output
```
false
true
```


## hashCode

객체의 주소값을 해싱한 값을 반환한다. 마찬가지로 서로 다른 두 객체가 같은 해시값을 가질 일은 없다.



## toString

인스턴스에 대한 정보를 문자열로 반환한다. Object 클래스의 toStirng은 다음과 같이 구현되어 있다.

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

해당 클래스의 toString 메서드를 따로 오버라이딩하지 않으면 Object 클래스의 toString 메서드가 사용되므로 결과값은 클래스이름@16진수 해시코드로 나타난다.


## clone

자신을 복제하여 새로운 인스턴스를 생성한다. clone 메서드는 Cloneable 인터페이스를 구현한 객체에서만 사용 가능하며, 그 외의 클래스에서 사용하려는 경우 CloneNotSupportedException이 발생한다.

clone은 인스턴스의 값만 복사하기 때문에 참조 타입의 인스턴스 변수가 있는 클래스는 완전한 복제가 이루어지지 않는다. 무슨 말인지 예시를 통해 알아보자.

```java
public class ObjDemo {

    public static void main(String[] args) {
        Circle c1 = new Circle(1, 1, 4);
        Circle clone = c1.clone();
        clone.center.x *= 2;
        clone.center.y *= 2;
        clone.r *= 2;

        System.out.println("c1 : " + c1.toString());
        System.out.println("clone : " + clone.toString());
    }

}

class Circle implements Cloneable {
    Point center;
    int r;

    Circle(int x, int y, int r) {
        center = new Point(x, y);
        this.r = r;
    }

    @Override
    public Circle clone() {
        Object obj = null;
        try {
            // clone을 오버라이딩 할 때는 예외 처리를 해줘야한다.
            obj = super.clone();
        } catch (CloneNotSupportedException e) { }

        return (Circle) obj;
    }

    @Override
    public String toString() {
        return "x = " + center.x + ", y = " + center.y + ", r = " + r;
    }
}

class Point {
    int x, y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

<br>

출력 결과

```
c1 : x = 2, y = 2, r = 4
clone : x = 2, y = 2, r = 8
```

Circle은 Point 객체를 참조하는 변수인 center와 int형 변수 r을 가지고 있다. clone 메서드를 오버라이딩하고, 객체의 정보를 보기 위해 toString도 오버라이딩 했다.

(x, y, r) = (1, 1, 4) 인 Circle 인스턴스 c1을 생성하고 이를 복제한 인스턴스 clone을 만들었다.

clone 인스턴스는 x, y, r 각각의 값에 두배를 해주고, c1과 clone을 출력했다.

결과값을 보면 c1의 x, y값에 변화가 생긴 것을 확인할 수 있다.

그림으로 나타내보면 이렇다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbpn33C%2FbtqTh0fU7oc%2Fl9ImYZgZmUnPomR6pGiC9k%2Fimg.png)

clone 인스턴스가 복제될 때, center 변수가 참조하고 있는 주소를 그대로 복사해왔기 때문에, clone 인스턴스의 center가 가리키고 있는 객체는 c1이 가리키고 있는 객체와 똑같다. 그래서 clone에서 center를 참조하던, c1에서 center를 참조하던 가리키는 값이 똑같기 때문에, clone에서 값을 변경한 것이 c1에도 적용이 된 것이다.

이를 얕은 복사(Shallow Copy)라고 한다.

해당 객체가 참조하고 있는 객체의 값까지 완전히 복사하려면(깊은 복사를 하려면) 다음과 같이 코드를 수정해야 한다.

```java
public class ObjDemo {

    public static void main(String[] args) {
        Circle c1 = new Circle(1, 1, 4);
        Circle clone = c1.clone();
        clone.center.x *= 2;
        clone.center.y *= 2;
        clone.r *= 2;

        System.out.println("c1 : " + c1.toString());
        System.out.println("clone : " + clone.toString());
    }

}

class Circle implements Cloneable {
    Point center;
    int r;

    Circle(int x, int y, int r) {
        center = new Point(x, y);
        this.r = r;
    }

    @Override
    public Circle clone() {
        Object obj = null;
        try {
            // clone을 오버라이딩 할 때는 예외 처리를 해줘야한다.
            obj = super.clone();
        } catch (CloneNotSupportedException e) { }
        Circle circle = (Circle) obj;
        circle.center = new Point(this.center.x, this.center.y);
        return circle;
    }

    @Override
    public String toString() {
        return "x = " + center.x + ", y = " + center.y + ", r = " + r;
    }
}

class Point {
    int x, y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

<br>

출력 결과

```
c1 : x = 1, y = 1, r = 4
clone : x = 2, y = 2, r = 8
```

## getClass

자신이 속한 클래스의 Class 객체를 반환하는 메서드이다. Class 객체는 클래스의 모든 정보를 담고 있으며, 클래스 당 1개만 존재한다. 클래스 객체는 클래스 파일(*.class)이 클래스로더(classLoader)에 의해 메모리에 올라갈 때 자동으로 생성된다.

클래스로더는 런타임에 필요한 클래스를 동적으로 메모리에 로드하는 역할을 한다. 먼저 클래스 객체가 이미 메모리에 올라와 있는지 확인하고 없으면 클래스패스(classpath)에 지정된 경로를 따라서 클래스 파일을 찾는다. 못 찾으면 ClassNotFountException이 발생하고, 찾으면 해당 클래스 파일을 읽어서 Class 객체로 반환한다.

Class 객체에 대한 참조를 얻는 방법은 여러 가지가 있다.

```
Class classObj = new Card().getClass();  // 생성된 객체로부터 얻는 방법
Class classObj = Card.class;             // 클래스 리터럴(*.class)로부터 얻는 방법
Class classObj = Class.forName("Card");  // 클래스 이름으로부터 얻는 방법
```

### 참고자료

자바의 정석(남궁성 저)