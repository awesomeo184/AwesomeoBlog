---
title: '[Java Study 3주차] 자바 연산자' 
date: 2020-12-31 12:13:27 
category: 'Java-Basic' 
thumbnail: { thumbnailSrc } 
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

* 목표: 자바가 제공하는 다양한 연산자를 학습하세요.

# 연산자

## 연산자 종류

자바에는 아래와 같은 연산자들이 존재한다. 연산의 우선순위가 높은 순서대로 나열되어 있다. Associativity는 연산의 방향을 나타낸다. L은 왼쪽에서 오른쪽으로 연산이 진행되고 R은 오른쪽에서 왼쪽으로 연산이
진행된다는 뜻이다.

|Associativity|Operator|Operand Types|Operation Performed|
|---|---|---|---|
|L   |.   |object, member   |객체의 멤버에 접근   |
|   |[ ]   |array, int   |배열의 요소에 접근   |
|   |(args)   |method, arglist   |메서드 호출   |
|   |++, --   |variable   |전위 증감   |
|R   |++, --   |variable   |후위 증감   |
|   |+, -   |number   |unary plus, unary minus   |
|   |~   |integer   |1의 보수 연산   |
|   |!   |boolean   |논리 부정   |
|R   |*new*   |class, arglist   |객체 생성   |
|   |(type)   |type, any   |형 변환   |
|L   |*, /, %   |number, number   |곱셈, 나눗셈, 나머지 연산   |
|L   |+, -   |number, number   |덧셈, 뺄셈   |
|   |+   |String, any   |문자열 연결   |
|L   |<<   |integer, integer   |left shift   |
|   |> >   |integer, integer   |right shift with sign extension   |
|   |> > >   |integer, integer   |right shift with zero extension   |
|L   |<, <=   |number, number   |less than, less than or equal   |
|   |> . >=   |number, number   |greater than, greater than or equal   |
|   |instanceof   |reference, type   |타입 비교   |
|L   |==   |primitive, primitive   |equal(identical value)   |
|   |!=   |primitive, primitive   |not equal   |
|   |==   |reference, reference   |equal(같은 객체에 대한 참조)   |
|   |!=   |reference, reference   |not equal   |
|L   |&   |integer, integer   |bitwise AND   |
|   |&   |boolean, boolean   |boolean AND   |
|L   |^   |integer, integer   |bitwise XOR   |
|   |^   |boolean, boolean   |boolean XOR   |
|L   |\|   |integer, integer   |bitwise OR   |
|   |\|   |boolean, boolean   |boolean OR   |
|L   |&&   |boolean, boolean   |conditional AND   |
|L   | \|\|   |boolean, boolean   |conditional OR   |
|R   |?:   |boolean, any, any   |논리 삼항 연산   |
|R   |=   |variable, any   |할당   |
|   |*=, /=, %=   |variable, any   |연산 및 할당   |
|   |+=, −=, <<=   |   |   |
|   |> > =, >>>=   |   |   |
|   |&=, ^=, \|=    |   |   |

출처 : Java in a Nutshell (O'REILLY)

## 산술 연산자

산술 연산을 수행한다. 어려운 부분은 아니니 몇 가지 주의할 사항만 확인하고 넘어가도록 하자. 프로그래밍을 접하지 않은 사람은 `%`가 낯설 수 있는데, `%`는 두 수를 나눈 나머지를 반환하는 연산자이다. 연산에는
타입 프로모션 규칙이 적용된다. 즉 서로 다른 자료형을 연산할 경우, 결과값은 표현 범위가 더 넓은 자료형으로 타입 프로모션이 일어난다.

'+' 연산자로 문자열 연결(concatenation)이 가능하다. 연산은 왼쪽에서 오른쪽으로 진행되며, 문자열이 나타날 경우, 문자열로 변환되어 계산된다. 예시를 보자.

```java
public static void main(String[] args) {
    String a = "가";
    String b = "나";
    System.out.println(a + b);    //"가나"
}
```

문자열을 덧셈 연산하면 두 문자를 이어붙인 **새로운 문자열 객체**가 반환된다. 다음 코드는 어떤 값을 반환할까?

```java
public static void main(String[] args) {
    System.out.println("" + 1 + 2);
}
```

연산은 왼쪽에서 오른쪽으로 일어나고 문자열과 숫자를 더할 경우 숫자를 문자열로 변환하여 계산하기 때문에, 문자열 "1" 과 "2"가 합쳐져서 문자열 "12"가 출력된다.

그렇다면 다음 연산은 어떤 결과값을 반환할까.

```java
public static void main(String[] args) {
    System.out.println(1 + 2 + "");
}
```

아까 말한대로 왼쪽에서 오른쪽으로 연산이 되기 때문에 1 + 2가 먼저 계산이 된 후 빈 문자열이 더해져 결과적으로 문자열 "3"이 출력된다.

산술 연산에서 주의할 점 다른 하나는 나눗셈 연산이다.

파이썬에는 반환 타입이 실수인 `/`와 반환 타입이 정수인 `//`가 따로 존재하지만, 자바에는 `/` 하나만 존재한다. 앞서 말했듯이 연산에는 타입 프로모션이 적용된다. 정수에 정수를 나누면 반환값도 정수이다.

```java
public static void main(String[] args) {
    int a = 3;
    int b = 2;
    System.out.println(a / b);    // 1 (int)
}
```

만약 값이 1.5가 나오게 만들고 싶으면 피연산자 중 하나가 실수가 되도록 만들어서 타입 프로모션이 일어나도록 해줘야한다.

```java
public static void main(String[] args) {
    double a = 3;
    int b = 2;
    System.out.println(a / b);    // 1.5 (double)
}
```

## 비트 연산자

비트 연산은 말 그대로 비트(0과 1)끼리의 연산이며, 우리가 익히 알고 있는 진리표대로 결과값을 반환한다.
0은 false, 1은 true와 대응된다.

<br>


* **NOT(~): 1의 보수 연산, 즉 각 비트를 반전시킨다.**

|operand|result|
|---|---|
|0|1|
|1|0|

<br>

* **AND(&): 둘 다 참이면 참**

|operand1|operand2|result|
|---|---|---|
|0|0|0|
|0|1|0|
|1|0|0|
|1|1|1|

<br>


* **OR(|): 둘 중 하나라도 참이면 참**

|operand1|operand2|result|
|---|---|---|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|1|

<br>

* **XOR(^): 두 값이 서로 다르면 참**

|operand1|operand2|result|
|---|---|---|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

<br>

해당 변수를 비트열로 바꾼 뒤 계산한다.

```java
public static void main(String[] args) {
    int a = 10;    // 0000 0000 ... 1010
    int b = 6;     // 0000 0000 ... 0110
    System.out.println(~a);       // -11:  1111 1111 ... 0101
    System.out.println(a & b);    //  2:   0000 0000 ... 0010
    System.out.println(a | b);    // 14:   0000 0000 ... 1110
    System.out.println(a ^ b);    // 12:   0000 0000 ... 1100
}
```

## 관계 연산자

두 항 사이의 관계에 대한 참, 거짓을 나타낸다.

주의할 것은 **==** 와 **!=** 연산자인데, 원시 자료형끼리의 연산에서는 두 값이 같은지를 나타내고 참조 자료형끼리의 연산에서는 두 값이 같은 참조값을 가지고 있는지를 나타낸다.
예시로 확인해보자

```java
public static void main(String[] args) {

    int a  = 3;
    int b = 3;

    boolean primitive = a == b;   // true

    String str1 = "Hello";
    String str2 = "Hello";

    boolean reference = str1 == str2;   // false
}
```

[이전 포스팅](https://awesomeo184.netlify.app/Java-Basic/Java_DataType/#%EC%B0%B8%EC%A1%B0-%EC%9E%90%EB%A3%8C%ED%98%95reference-type)에서 살펴봤듯이
`str1`과 `str2`는 서로 다른 객체를 가리키고 있으므로, **==**의 연산 결과로`false`를 반환받는다.

<br>

`instanceof` 연산자는 좌항에 객체, 우항에 클래스가 들어는데, 좌항의 객체가 우항의 클래스에 속한다면 참을 반환한다.

```java
public class Main {

    public static void main(String[] args) {
        A a = new A();
        System.out.println(a instanceof A);    // true
    }
}

class A {

}
```

## 논리 연산자

논리 연산은 기본적으로 비트 연산과 같지만 피연산자가 불린이라는 점이 다르다.

```java
public static void main(String[] args) {
    System.out.println(true && true);    // true
    System.out.println(true && false);   // false
    System.out.println(true || true);    // true
    System.out.println(true || false);   // true
}
```

논리 연산에서 `&&` 대신 `&` 하나만 써도 같은 결과를 반환한다. 차이점은 `&&`는 좌항이 거짓이면 우항을 판단하지 않지 않고 거짓을 반환한다는 것이다.
AND 연산은 둘 중 하나라도 거짓이면 거짓이기 때문에 굳이 나머지 하나를 판단할 필요가 없기 때문이다.
반면 `&`는 좌항이 거짓이라도 우항을 검사한다.

마찬로 `||`는 좌항이 참이면 우항을 판단하지 않고 참을 반환한다. `|`는 좌항이 참이라도 우항을 검사한다.

예를 들어 다음과 같은 코드가 있을 때,

```java
public static void main(String[] args) {
    int i = 0;

    if (true || i++ == 0) { }
    
    System.out.println(i);

    if (true | i++ == 0) {}

    System.out.println(i);

    if (false && i++ == 0) {}

    System.out.println(i);

    if (false & i++ == 0) {}

    System.out.println(i);
}
```

결과 값은 다음과 같다.

    0
    1
    1
    2

`&&`나 `||`가 사용된 조건식에서는 첫 번째 operand로 결과 값을 도출할 수 있으면 두 번째 operand인 `i++ == 0`를 확인하지 않기 때문이다.

## 할당 연산자

대입 연산자 혹은 할당 연산자라고 부른다. 연산자를 기준으로 왼쪽에 변수, 오른쪽에 리터럴이 오며 변수에 리터럴을 할당하는 역할을 한다. 기본적으로 다음과 같이 사용한다.

```java
int a = 3;
```

위 표현식은 아래를 축약한 형태이다.
```java
int a;
a = 3;
```

대입 연산자를 통해 변수에 실질적인 값을 할당해야 비로소 변수를 사용할 수 있게 된다. 이를 `초기화`라고도 한다. 할당은 여러번 일어날 수 있으며 가장 마지막에 일어난 할당 연산이 최종 값이 된다. 예를 들어
```java
int a = 3;

{... ...}

a = 5;
```

이렇게 처음 변수 a를 초기화한뒤 , a에 5를 다시 할당하면 이후로는 a의 값이 5가 된다. 새로 값을 할당하기 이전 문장에서는 기존 값이 사용된다.

<br>

할당 연산자는 산술, 비트, 쉬프트 연산자와 함께 사용할 수 있다. 예를 들어
```java
int a = 3;
a = a + 5;    // a == 8
```

위 문장은 아래와 같이 축약될 수 있다.

```java
int a = 3;
a += 5;
```

변수에 특정 연산을 한 후 다시 변수에 대입하는 형태는 모두 이런 형태로 바꿀 수 있다 예를 들어

```java
int a = 10;
a = a / 5;    // a == 2
```
위 문장도 마찬가지로 `a /= 5`로 축약할 수 있으며, 산술, 비트, 쉬프트 연산자 모두 다 사용이 가능하다.

<br>

아래는 1부터 10까지의 합을 출력하는 예제이다.

```java
public static void main(String[] args) {
    int a = 0;
    for (int i = 1; i < 11; i++) {
        a += i
    }
    System.out.println(a);    // 55
}
```

## 화살표 연산자 (->)

화살표 연산자는 자바 8부터 추가된 기능인데, 람다 표현식(Lambda expression)에서 사용한다.

추후에 람다 표현식에 대해 다룰 때 자세히 다루도록 하고 여기서는 간단히 사용법만 살펴보고 넘어간다.

우선 람다식은 메서드를 간단한 식으로 표현한 것이다.

```java
int add (int a, int b) {
    return a + b;
}
```

위의 메서드는 다음과 같이 바꿔 표현할 수 있다.

```java
(a, b) -> a + b
```

## 3항 연산자

3항 연산자는 조건식의 결과에 따라 다른 값을 반환하는 연산자이다. 형태는 다음과 같다.

    조건식 ? 식1 : 식2

조건식이 참이면 식1을 실행하고, 거짓이면 식2를 실행한다. 식이 아니라 값이 와도 된다.

```java
public class ConditionOperator {

    public static void main(String[] args) {
        max(10, 15);
    }
    
    public void max(int a, int b) {
        if (a > b) {
            System.out.println(a);
        } else {
            System.out.println(b);
        }
    }
}
```

위 메서드는 두 정수를 입력받아 더 큰 값을 출력하는 메서드이다. 이는 다음과 같이 줄여서 표현할 수 있다.

```java
public class ConditionOperator {

    public static void main(String[] args) {
        max(10, 15);
    }
    
    public void max(int a, int b) {
        System.out.println(a > b ? a : b);
    }
}
```

여담으로, 우아한 테크코스 프리코스 과정에 참여했을 때, 과제의 요구 사항 중에 삼항 연산자를 사용하지 말라는 항목이 있었다. 
가독성 때문에 그런 것으로 추측하는데, 개인적으로 정말 3항 연산자가 if문보다 가독성이 떨어지는지는 잘 모르겠다.


