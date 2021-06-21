---
title: '[Java Study 5주차] 클래스'
date: 2021-01-07 13:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

<br>

* 목표: 자바 클래스에 대해 학습하세요.

공부할 것

* 클래스 정의하는 방법
* 객체 만드는 방법 (new 키워드 이해하기)
* 메소드 정의하는 방법
* 생성자 정의하는 방법
* this 키워드 이해하기

# 클래스를 정의하는 법

객체지향의 관점에서 클래스는 객체를 생성하는 설계도이다. 프로그래밍 관점에서 클래스는 연관된 데이터와 함수의 집합이다. 자바 파일(.java) 안에 클래스를 정의할 때는 다음과 같은 규칙을 따라야한다.

public class가 존재하는 경우 소스파일 명과 퍼블릭 클래스의 이름은 일치해야 한다.

소스파일 하나에는 여러 클래스가 정의될 수 있지만 public class는 하나여야 한다.

public class가 존재하지 않는 경우 소스파일 명은 내부에 정의된 클래스들의 이름 중 하나로 한다.

클래스를 만드는 것은 8개의 프리미티브 타입과 함수를 조합해서 새로운 데이터 타입을 만드는 것과 같다. 프로그래밍 언어에서 사용자가 여러 변수들을 묶어서 하나의 타입을 새로 정의하는 것을 사용자정의 타입(user-defined type)이라고 하는데, 자바에서는 클래스가 사용자정의 타입이다.

클래스는 필드와 메서드로 구성된다. 필드는 해당 클래스에서 사용할 변수들을 의미한다.

예를 들어, TV 라는 타입을 만들고 싶다면 클래스를 다음과 같이 정의할 수 있다

> Tv.java

```java
public class Tv {
    boolean power;    // 전원 (on/off)
    int channel;      // 채널
  
    void power() {
        power = !power;
    }
  
    void channelUp() {
        ++channel;
    }
      
    void channelDown() {
        --channel;
    }
}
```

여기서 power와 channel은 **필드** 혹은 **멤버 변수**라고 부르고, power(), channelUp(), channerDown()은 **메서드**라고 부른다. 필드와 메서드를 합쳐서 **클래스의 멤버**라고 부른다.

# 객체를 만드는 법

클래스는 객체를 만들기 위한 설계서라고 했다. 클래스를 통해 인스턴스를 만드는 법은 다음과 같다.

```
클래스명 변수명;
변수명 = new 클래스명();
```

<br>

물론 다음과 같이 축약할 수 있다.

```
클래스명 변수명 = new 클래스명();
```

<br>

위에서 정의한 Tv클래스를 선언하고 사용하는 예제이다.

```java
public static void main(String[] args) {
    Tv t1;
    t1 = new Tv();
    t1.power();
    t1.channel = 10;
    t1.channelUp();
}
```

`new` 키워드는 메모리 공간에 인스턴스를 생성하는 연산자이다. 코드를 한줄 한줄 분석해보자.

Tv t1;

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSmyHt%2FbtqS1WjmZp2%2FkoYP16JZqMWrl2zKNDq0f0%2Fimg.png)

Tv 클래스 타입의 참조변수 t1을 선언한다. 메모리에 참조변수를 위한 공간이 마련된다.

<br>

new Tv;

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbsjIQP%2FbtqSSdtrXk3%2FJ5qLpQze9t0tiECLqaXSDk%2Fimg.png)

new keyword를 통해 인스턴스를 생성한다. 메모리의 빈 공간에 생성된다(여기선 주소가 0x100인 곳에 생성되었다고 가정). 이때 멤버 변수는 기본값으로 초기화된다.

<br>

t1 = new Tv();

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpFrO0%2FbtqSNmYYke0%2FUZZ3ydrzGKolkkPj4DGQa1%2Fimg.png)

대입연산자를 통해 인스턴스와 참조변수가 연결된다. 객체의 주소값(0x100)이 참조변수에 저장된다. 인스턴스를 다루기 위해서는 참조변수가 반드시 필요하다.

<br>

t1.power();

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbCugy9%2FbtqS1WKp9mz%2FWPWbOYVFIJfsv4VE1bs5L0%2Fimg.png)

t1을 통해 power() 메서드를 호출한다. 메서드는 해당 인스턴스의 멤버 변수에 저장되어있는 값을 변화시킨다.

<br>

t1.channel = 10;

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbR7qtH%2FbtqS1VdICXi%2FQ1Nqiwb2BTHUaK6ndK8jfk%2Fimg.png)

.(dot) 연산자를 통해 인스턴스의 멤버에 접근할 수 있다.

참조변수.멤버변수 형식으로 접근한다. 

# 메서드 정의하는 방법

메서드는 크게 선언부와 구현부로 나눌 수 있다. 선언부는 메서드의 이름과 반환 타입 그리고 매개변수를 지정한다.

구현부는 메서드가 어떻게 동작할 것인지를 지정한다. 보통 input을 받아서 일정한 처리를 하고 output을 반환하는 것을 함수(function)이라고 하는데, 
클래스 내에 정의된 함수를 메서드(method)라고 부른다(일반적으로 이렇게 정의할 수 있다. 객체 지향적 관점에서는 조금 다른 관점으로 정의할 수 있는데, 다른 포스팅에서 정리해봐야겠다). 

Java는 모든 것이 클래스로 구성되어 있으므로 오로지 메서드만 존재한다.

```
반환타입 메서드이름 (타입 변수명, 타입 변수명)    // 선언부
{
	// 메서드 호출 시 수행될 코드 (구현부)
}
```

다음은 두 개의 정수를 매개변수로 받아서 두 값을 더한 결과를 반환하는 메서드이다.

```java
int add(int a, int b) {
    return a + b;
}
```

<br>

메서드의 용도에 따라서 매개변수 혹은 반환값이 없을 수도 있다. 반환값이 없는 경우 반환 타입에 void라고 명시해준다.

```java
void printAddValue(int a, int b) {
    System.out.println(a + b);
}
```

<br>

정의된 메서드는 호출을 해줘야만 실행된다.

```java
public static void main(String[] args) {
    int result = add(1, 3);   // result = 4
    printAddValue(4, 5);      // 9를 출력
}
```

<br>


# 생성자 정의하는 법

생성자는 인스턴스가 생성될 때 호출되는 인스턴스 초기화 메서드이다. 인스턴스 변수의 초기화 작업에 주로 사용되며, 인스턴스 생성 시에 실행되어야 할 작업을 위해서도 사용된다.

생성자의 조건은 다음과 같다.

* 생성자의 이름은 클래스의 이름과 같아야 한다.
* 리턴값이 없다.


주의할 점은 생성자가 인스턴스를 생성하는 역할을 하는 것이 아니라는 것이다. 인스턴스 생성은 new 연산자를 통해 이루어지며 생성자는 인스턴스 변수들을 초기화하는 역할만 한다.

구체적 예시를 통해 알아보자.
```java{4}
public class Main {
    public static void main(String[] args) {
        Tv tv = new Tv();
        tv.setColor("white");
    }
}

class Tv {
    private String color;
    private boolean power;
    private int channel;

    public void setColor(String TvColor) {
        color = TvColor;
    }
}
```
위 코드는 생성자를 이용하지 않고, setColor라는 메서드를 이용해서 멤버 변수를 초기화해준 모습이다. 생성자를 이용하면 코드를 다음과 같이 수정할 수 있다.

```java{3}
public class Main {
    public static void main(String[] args) {
        Tv tv = new Tv("white");
    }

class Tv {
    private String color;
    private boolean power;
    private int channel;

    Tv(String TvColor) {
        color = TvColor;
    }
}
```

보면 알겠지만 new Tv("white") 이 부분이 생성자를 호출하는 코드이다.

첫번째 코드에서는 생성자를 따로 작성하지 않았는데도, new Tv()라는 코드가 작성이 가능했는데 이는 생성자를 따로 지정해주지 않을 경우 컴파일러가 알아서 기본 생성자(default constructor)를 추가해주기 때문이다. 첫번째 코드가 컴파일 될 때는 사실 다음과 같은 정보가 전달되는 것이다.

```java{13}
public class Main {
    public static void main(String[] args) {
        Tv tv = new Tv();
        tv.setColor("white");
    }
}
    
class Tv {
    private String color;
    private boolean power;
    private int channel;
    
    Tv() {}   // 이 부분을 컴파일러가 자동으로 추가해준 것!
    
    public void setColor(String TvColor) {
        color = TvColor;
    }
}
```

단 기본 생성자는 클래스 내에 생성자가 없는 경우에만 추가된다. **생성자가 단 하나라도 있으면 기본 생성자는 추가되지 않는다.** 
또한 클래스의 접근 제어자가 public인 경우 생성자의 접근 제어자에도 public이 추가된다.

# this 키워드 이해하기

생성자는 여러 개를 추가할 수 있으며 한 생성자가 다른 생성자를 호출할 수도 있는데 이 때 this()를 사용한다.

```java{18}
public class Main {
    public static void main(String[] args) {
        Car car1 = new Car("black", 4);
        Car car2 = new Car();
    }
}

class Car {
    private String color;    // 차 색깔
    private int door;        // 문 개수

    Car(String carColor, int carDoor) {
        color = carColor;
        door = carDoor;
    }

    Car() {
        this("white", 4);
    }
}
```

car2 인스턴스는 생성자에 아무런 인자도 전달하지 않는데, 이 경우 this()를 통해서 Car(String carColor, int carDoor)를 호출해서 "white"와 4로 멤버 변수를 초기화한다.

논리적으로 Car("white", 4); 를 해준것과 똑같지만 생성자에서 다른 생성자를 호출할 때는 생성자 이름을 호출하는게 아니라 `this()`로 호출한다. 
그럼 생성자가 여러 개 있을 때, 이름이 다 똑같은데 어떻게 구분하느냐? 생성자는 매개변수의 개수와 타입으로 구분된다. 
즉 this 안에 String 하나와 int 하나를 전달했기 때문에 컴파일러가, 어떤 생성자인지 찾아서 호출을 해준 것이다.

<br>

위 코드에서 생성자의 매개변수의 이름을 carColor, carDoor로 지었는데, 그냥 color, door라고 하면 안될까?

```java{13,14}
public class Main {
    public static void main(String[] args) {
        Car car1 = new Car("black", 4);
        Car car2 = new Car();
    }
}

class Car {
    private String color;    // 차 색깔
    private int door;        // 문 개수

    Car(String color, int door) {
        color = color;
        door = door;
    }

    Car() {
        this("white", 4);
    }
}
```

위 코드는 컴파일 에러는 발생하지 않지만 car1이 제대로 초기화되지 않는다. 
왜냐하면 매개변수를 가리키는 color와 멤버 변수를 의미하는 color가 구분이 되지 않기 때문이다. 이때 자신의 인스턴스를 가리키는 참조변수 `this`를 사용한다.

```java{13,14}
public class Main {
    public static void main(String[] args) {
        Car car1 = new Car("black", 4);
        Car car2 = new Car();
    }
}

class Car {
    private String color;    // 차 색깔
    private int door;        // 문 개수

    Car(String color, int door) {
        this.color = color;
        this.door = door;
    }

    Car() {
        this("white", 4);
    }
}
```

이렇게 this.멤버변수 형태로 적어주면 왼쪽의 color는 멤버 변수 color를 의미한다는 것을 컴파일러에게 알려줄 수 있다. 
주의할 점은 `this()`는 생성자이고, `this`는 자기 자신의 인스턴스를 가리키는 참조변수로 **이름은 같지만 용도가 완전히 다르다는 것**이다.

### 참고자료

자바의 정석(남궁성 저)