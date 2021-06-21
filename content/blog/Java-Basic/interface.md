---
title: '[Java Study 8주차] 인터페이스'
date: 2021-01-10 11:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

<br>

* 목표: 자바의 인터페이스에 대해 학습하세요.

<br>

공부할 것

* 인터페이스 정의하는 방법
* 인터페이스 구현하는 방법
* 인터페이스 레퍼런스를 통해 구현체를 사용하는 방법
* 인터페이스 상속
* 인터페이스의 기본 메소드 (Default Method), 자바 8
* 인터페이스의 static 메소드, 자바 8
* 인터페이스의 private 메소드, 자바 9

# 인터페이스 정의 및 구현 방법

## 인터페이스란?

인터페이스는 일종의 추상 클래스이지만 추상 클래스보다 추상도가 더 높다. 인터페이스는 몸통을 가진 메서드나 멤버 변수를 가질 수 없고 오직 상수와 추상 메서드만 선언이 가능하다. 
(Java 8, Java 9에서 default, static, private 메서드가 추가되면서 몸통을 가진 메서드를 작성할 수 있게 되었다.)

## 인터페이스 작성

인터페이스는 `interface` 키워드로 선언한다.

```java
interface 인터페이스이름 {
    public static final 타입 상수이름 = 값;
    public abstract 리턴타입 메서드이름(매개변수목록);
}
```

<br>

작성하는 방법은 여느 클래스와 같으나 몇 가지 제약 사항이 있다.
* 모든 멤버변수는 public static final 이어야 하며, 이를 생략할 수 있다.
* 모든 메서드는 public abstract 이어야 하며, 이를 생략할 수 있다.
  (단 자바8, 9에 추가된 default, static, private 메서드는 제외)
  제어자를 생략할 경우 컴파일 단계에서 컴파일러가 자동으로 추가한다.
  
<br>

## 인터페이스의 상속

인터페이스는 오로지 인터페이스로부터만 상속을 받을 수 있으며, 클래스와 달리 다중 상속이 가능하다.

>참고: 인터페이스는 클래스와 달리 Object같은 최고 조상이 없다.

```java
interface Movable {
    void move();
}

interface Attackable {
    void attack(Unit unit);
}

interface Fightable extends Movable, Attackable {}
```

## 인터페이스의 구현

인터페이스는 '상속받는다'라고 표현하지 않고 '구현한다'라고 표현하며 ,implements 키워드를 사용한다. 하나의 클래스는 여러 인터페이스를 구현할 수 있으며, 아래처럼 상속과 구현을 동시에 할 수도 있다.

```java
public class Tank extends Uint implements Fightable {
... ...
}
```

또, 인터페이스의 메서드는 기본적으로 public abstract이기 때문에 인터페이스를 구현한 클래스는 해당 인터페이스의 추상 메서드를 모두 오버라이딩해야 하며 접근 제어자가 반드시 public 이어야 한다.

## 인터페이스 참조를 통해 구현체를 사용하는 방법

하나의 객체가 여러 타입을 가질 수 있는 것을 다형성(polymorphism)이라고 한다.

자바에서는 부모 클래스 타입의 참조 변수로 자식 클래스 타입의 인스턴스를 참조할 수 있도록 함으로써 다형성을 실현하는데, 인터페이스로도 이것이 가능하다.

Fightable 인터페이스를 구현한 클래스 Fighter가 있을 때, 다음과 같이 Fightable 타입의 참조 변수로 Fighter 인스턴스를 참조하는 것이 가능하다.

```java
Fightable f = (Fightable) new Fighter();
```

또는

```java
Fightable f = new Fighter();
```

<br>

이 때, 참조 변수 f로는 Fightable 인터페이스에 정의된 멤버들만 호출 가능하다.

매개 변수로 Fightable을 넘겨줄 수도 있다. 이때 해당 인터페이스를 구현한 객체의 인스턴스들만 매개변수로 전달될 수 있다.

```java
void attack (Fightable f) {
  ... ...
}
```

<br>

그리고 리턴 타입으로 인터페이스를 지정해줄 수도 있다. 이때 리턴 값은 해당 인터페이스를 구현한 클래스의 객체만 가능하다.

```java
Fightable method () {
  ... ...
  return new Fighter();
}
```

다음은 매개 변수로 넘어온 String이 XML 타입이면 XMLParser를 반환하고, HTML 타입이면 HTMLParser를 반환하는 메서드를 다형성을 활용하여 구현한 예시이다.

```java
interface Parseable {
    //구문 분석 작업을 한다.
    void parse(String fileName);
}

public class ParseManager {
    public Parseable getParser(String type) {
        // 타입은 XML과 HTML 두 가지만 존재한다고 가정 
        if (type.equals("XML")) {
            return new XMLParser();
        } else {
            return new HTMLParser();
        }
    }
}

class XMLParser implements Parseable {
    @Override
    public void parse(String fileName) {
        System.out.println("XML PARSING ...");
    }
}

class HTMLParser implements Parseable {
    @Override
    public void parse(String fileName) {
        System.out.println("HTML PARSING ...");
    }
}
```

# default method & static method

Java8부터 인터페이스에 디폴트 메서드와 스태틱 메서드를 추가할 수 있게 되었다. 

이전까지 인터페이스에는 추상 메서드만 선언이 가능했다. 추상 메서드는 이를 구현하는 클래스에서 반드시 오버라이드 해야한다는 제약이 있지만
**디폴트 메서드와 스태틱 메서드가 추가되면서 인터페이스에도 몸통(구현부)를 가진 메서드를 작성할 수 있게 되었다.**

<br>

**디폴트 메서드**는 Java 8에 스트림 API를 도입하기 위해 함께 추가되었는데, **하위 호환성**을 목적으로 도입되었다. 

Collection 인터페이스에 Stream API의 메서드를 추가해야 하는데, 인터페이스에 메서드를 추가하면 Collection을 구현한
모든 클라이언트가 해당 메서드를 오버라이드해야 하기 때문에 하위 호환성이 깨지게 된다. 자바 개발자들은 이를 피하기 위한 목적으로 디폴트 메서드를 도입하고 Stream API의 메서드들을 디폴트 메서드로 추가하였다.

<br>

사실 **스태틱 메서드**는 인스턴스와 상관이 없기 때문에 예전부터 인터페이스에 추가되지 못할 이유가 없었다. 그러나 규칙의 단순화를 위해 인터페이스의 모든 메서드는 추상 메서드여야 한다는 규칙에 예외를 두지 않았다. 그래서 인터페이스와 관련된 스태틱 메서드는 따로 클래스로 만들어서 관리했는데, 대표적인 것이 Collections다.

java.util.Collection은 인터페이스이고 이와 관련된 static method들은 Collections라는 클래스에 따로 들어가있다. 만약 처음부터 인터페이스에 static method가 있었다면 Collections 클래스는 존재하지 않았을 것이다.

<br>

디폴트 메서드와 스태틱 메서드 역시 접근 제어자는 public이며 생략 가능하다.디폴트 메서드는 추상 클래스의 디폴트 메서드와 같이 몸통을 구현할 수 있으며 이를 구현한 클래스에서 반드시 오버라이딩하지 않아도 된다. 디폴트 메서드가 추가되면서 사실상 인터페이스와 추상 메서드의 차이가 거의 사라진 것 같은데, 나중에 이에 대해 정리해봐야겠다.

디폴트 메서드를 사용할 때 주의할 점 한 가지는 두 개의 인터페이스를 구현했는데 **두 인터페이스의 디폴트 메서드 시그니처가 겹친다면 이를 구현한 클래스에서 반드시 해당 메서드를 오버라이드 해야한다.** 

해당 인터페이스의 메서드를 `인터페이스이름.super.메서드()`로 호출하는 것도 가능하다.

```java
interface Dog {
    default void bark() {
        System.out.println("멍멍");
    }
}

interface Wolf {
    default void bark() {
        System.out.println("오우우~");
    }
}

public class WolfDog implements Dog, Wolf{
    @Override
    public void bark() {
        Dog.super.bark();
        Wolf.super.bark();
        System.out.println("멍멍 오우우~");
    }

    public static void main(String[] args) {
        WolfDog dog = new WolfDog();
        dog.bark();
    }
}
```

출력 결과

```
멍멍
오우우~
멍멍 오우우~
```

# private method

Java 9에 추가되었으며 default method의 로직 분리를 위해 사용될 수 있다. 당연히 오버라이딩될 수 없고 인터페이스 내부에서만 사용 가능하다.

### 참고자료

자바의 정석(남궁성 저)