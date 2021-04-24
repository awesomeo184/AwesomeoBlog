---
title: '[Java Study 2주차] 자바 데이터 타입, 변수 그리고 배열'
date: 2020-12-29 15:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

* 목표: 자바의 프리미티브 타입, 변수 그리고 배열을 사용하는 방법을 익힙니다.

# 데이터 타입이란?
[이전 포스팅](https://awesomeo184.netlify.app/Java-Basic/DataType/)에서 데이터 타입이 무엇이고 어떤 기능을 하는지에 대해 알아본 적이 있다. 
간단하게 말하자면 데이터 타입이란 **메모리의 크기와 형태를 지정하기 위한 도구**이다.
이번에는 자바의 프리미티브 타입과 변수에 대해 알아보자.

## 원시 자료형(Primitive Data Type)
원시 자료형은 컴파일러 차원에서 미리 메모리의 크기와 형태를 지정해둔 것이다. 자바에는 8가지의 원시 자료형이 있다.

![3264570B-2D8C-4336-B91B-62F0B6253208](https://user-images.githubusercontent.com/63030569/115959839-2bbe0080-a549-11eb-8f61-65d2a6e64684.png)

원시 자료형의 이름과 크기는 외워둬야 한다. 특히 int가 4 bytes(1byte = 8 bits)라는 것 정도는 상식으로 알고 있어야 한다.

> 왜 int는 4 bytes일까? 간단히 말하면 32 bits 즉 4 bytes가 컴퓨터의 기본 단위로 계산하기 가장 좋았기 때문이다. [이 글](https://cs50.stackexchange.com/questions/2404/sizeofint-is-2-bytes-in-some-computers-while-it-is-4-bytes-in-most-modern-one)을 참조했다.

한 비트로 표현할 수 있는 정보의 양은 두 개다. 0이면 거짓, 1이면 참을 표현할 수 있다. 그래서 boolean의 크기는 1byte이다. 
정확히 말하면 1bit만 있으면 참, 거짓을 표현할 수 있지만 컴퓨터의 최소 저장 단위가 byte이기 때문에 byte를 사용한다.

int의 크기인 4 bytes로는 몇 개의 정보를 표현할 수 있을까? 4 bytes는 32 bits 이니까 2^32 개의 정보, 즉 0에서 2^32 -1 (4,294,967,295) 까지 표현할 수 있다. C에서 부호 없는 정수(unsigned int)의 표현 범위가 이에 해당한다. 그런데 Java에서는 unsinged int를 사용하지 않는다(Java 8부터 unsigned의 개념이 도입되었지만... 조금 애매하다). 양수와 음수를 모두 표현하려면 범위가 반으로 줄어들 것이다. 그래서 int의 표현 범위는 -2^31 (-2,147,483,648) ~ 2^31 -1 (2,147,483,647)이 된다. int의 표현 범위는 대략 21억 정도라고 기억해두시면 될 것 같다.

좀 더 자세히 다루자면, 컴퓨터는 음수를 표현하기 위해서 2의 보수(2's complement) 표현법을 사용한다. 2의 보수는 숫자의 각 비트를 반대로 뒤집고 1을 더하면 구할 수 있다. 따라서 int가 컴퓨터 메모리에 실제로 저장되는 형태는 다음과 같다. '='을 기준으로 왼쪽은 이진수, 오른쪽은 십진수이다. 잘 보면 음수의 최상단 비트(MSB)는 1로 시작한다는 것을 알 수 있다.

```
0000 0000 0000 0000 0000 0000 0000 0000 = 0
0000 0000 0000 0000 0000 0000 0000 0001 = 1
0000 0000 0000 0000 0000 0000 0000 0010 = 2
... ...
0111 1111 1111 1111 1111 1111 1111 1101 = 2,147,483,645
0111 1111 1111 1111 1111 1111 1111 1110 = 2,147,483,646
0111 1111 1111 1111 1111 1111 1111 1111 = 2,147,483,647
1000 0000 0000 0000 0000 0000 0000 0000 = -2,147,483,648
1000 0000 0000 0000 0000 0000 0000 0001 = -2,147,483,647
1000 0000 0000 0000 0000 0000 0000 0010 = -2,147,483,646
... ...
1111 1111 1111 1111 1111 1111 1111 1101 = -3
1111 1111 1111 1111 1111 1111 1111 1110 = -2
1111 1111 1111 1111 1111 1111 1111 1111 = -1
```

앞서 말했듯이 데이터 타입은 메모리의 크기와 형태를 결정한다. 위의 표를 보면 int와 float은 둘 다 4 bytes의 크기를 갖지만 표현하는 정보는 전혀 다르다. 정수가 메모리에 어떻게 저장되는지는 알겠고, 그렇다면 실수는 어떤 식으로 저장될까?

실수는 부호, 지수부, 가수부로 비트를 나눠서 데이터를 저장한다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJh4gp%2FbtqRQRKpgWw%2FuroXUrj6x9AH8dSkzpWYXK%2Fimg.png)

출처: http://www.tcpschool.com/cpp/cpp_datatype_floatingPointNumber


## 참조 자료형(Reference Type)

자바의 자료형은 원시 자료형과 참조 자료형 두 가지로 나뉜다. 위에서 언급한 8가지 자료형 외에는 모두 참조 자료형이다.
참조 자료형에는 배열과 객체(클래스)가 있다.

원시 자료형은 언어 차원에서 메모리 크기가 이미 정해져있다. 하지만 참조 자료형은 정해진 크기가 없다.
또 원시 자료형은 타입에 따라 메모리의 형태(위에서 살펴본 정수와 실수의 차이)가 있지만, 참조 자료형은 단순히 해당 객체가 존재하는 메모리 공간의 주소만 저장하고 있다.

그림으로 나타내면 이런 형태라고 할 수 있다.

![image](https://user-images.githubusercontent.com/63030569/115964247-7dbd5100-a55e-11eb-844c-0e580eb3a386.png)

> 배열의 요소 간의 주소 변위는 일정하지만 배열 자체의 주소와 첫번째 인덱스 주소의 변위는 일정하지 않다. 설명의 편의를 위해 대충 나타냈다...

**원시 자료형은 값을 저장하고, 참조 자료형은 객체의 주소를 저장한다.** 이 사실을 알면 다음과 같은 일이 왜 생기는지 설명할 수 있다.

```java
public static void main(String[] args) {
    int a = 5;
    int b = a;

    b = 3;
    System.out.println(a);  // 5
    System.out.println(b);  // 3

    int[] arr1 = {1, 2, 3};
    int[] arr2 = arr1;

    arr2[0] = 5;
    System.out.println(Arrays.toString(arr1));  // [5, 2, 3]
    System.out.println(Arrays.toString(arr2));  // [5, 2, 3]
}
```

arr2에는 arr1이 가리키고 있는 배열의 주소가 담겨있다. 즉 arr1과 arr2는 같은 배열을 가리키고 있기 때문에 
arr2를 통해 배열을 조작하더라도 arr1의 값이 변하는 것이다.

# 리터럴

데이터 타입이 메모리의 크기와 형태를 결정하기 위한 것이라면, 그 데이터 타입에 맞는 **실제 값**을 정의를 해주어 한다. 그 실제 값을 **리터럴**이라고 부른다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fmhaz7%2FbtqROd75tjd%2FSdLBaYYQ5JuQNaqRKTTuA1%2Fimg.png)

정수형과 실수형에는 여러 타입이 존재한다. 그래서 이들을 구분하기 위해 리터럴 뒤에 **접미사**를 붙여준다. 접미사는 long, float double에 붙여주는데, 실수형의 경우 double이 기본형이기 때문에 double에는 접미사를 붙이지 않아도 된다.

```java
long number1 = 15L;
float number2 = 3.14f;
double number3 = 1.52d; // 붙이지 않아도 됨.
```

접미사는 대소문자 상관없이 컴파일이 되지만, long의 경우 다른 문자와 헷갈리지 않기 위해 반드시 대문자 L을 사용하자.

또 JDK1.7부터 정수형 리터럴에 구분자('_')를 추가할 수 있어 숫자를 편하게 읽을 수 있다.
```java
long bigNumber = 2_150_000_000;
```

2, 8, 16 진수를 표현할 때는 리터럴에 접두사를 붙여준다.
```
int octNum = 010;    // 8진수 10을 의미. 앞에 숫자 0을 붙여준다.
int hexNum = 0x10;    // 16진수 10을 의미, 앞에 0x를 붙여준다.
int binNum = 0b10;    // 2진수 10을 의미, 앞에 0b를 붙여준다.
```

# 변수 선언 및 초기화

변수는 데이터를 담는 그릇이며 데이터 타입으로 생성된 메모리에 실제 값을 담고 프로그래머가 이를 이용할 수 있도록 해준다.
변수를 사용하려면 변수를 선언하고 초기화하는 과정이 필요하다. 우선 변수는 다음과 같이 선언한다.

```
자료형 변수명;
ex) int myNumber;
```

<br/>

변수 이름을 짓는데는 몇가지 규칙이 있다.
* 숫자로 시작할 수 없다. (`number1` 은 가능하나 `1number`는 불가능)
* 대소문자는 구분되며 길이에 제한이 없다. (`Company`와 `company`는 다른 이름으로 취급)
* 특수문자는 '$'와 '_'만 허용한다. (`$harp`는 가능하나 `#harp`는 불가능)
* 예약어를 사용할 수 없다.

<br/>

예약어(Keyword, reserved word라고도 함)는 자바에서 이미 사용하고 있는 이름을 말한다.
int, float, static, public, class, interface, if, else, goto 등이 있다. 프로그래밍을 하다 보면 예약어는 자연스레 습득하게 된다.

그 외 필수 사항은 아니지만 자바 프로그래머들 사이의 암묵적인 관례가 몇 가지 있다.
* 클래스 명은 반드시 대문자로 시작한다.
* 함수 명과 변수 명은 소문자로 시작한다.
* 여러 단어로 이루어진 경우 각 단어의 첫 글자를 대문자로 한다. (ex. MyClass, myVariable, myMethod)
* 상수는 대문자로 표현하고 여러 단어로 이루어진 경우 '_'로 구분한다. (ex. MAX_DISTANCE)

<br/>

변수는 데이터를 담는 그릇이라고 했다. 데이터를 담지 않으면 변수는 아무 쓸모가 없다. 
변수에 데이터를 넣는 것을 `할당(assignment)`이라고 하고, 변수를 선언한 후 처음 데이터를 할당하는 것을 `초기화(initialization)`라고 한다. 
선언된 변수는 사용하기 전에 반드시 초기화를 해주어야 한다. 변수는 다음과 같은 방법으로 선언 및 초기화를 할 수 있다.

<br>

1.선언과 초기화를 한 번에

```java
int a = 3;
```

2.선언과 초기화를 따로

```java
int a;
a = 3
```


3.여러 개의 변수를 한 번에 선언하고 초기화

```java
int a =3, b = 5;
```

4. 여러 변수를 한 번에 선언하고 따로 초기화

```java
int a, b;
a = 3;
b = 5;
```


5. 변수끼리 할당

```java
int a = 3;
int b = a; // b = 3
```

1과 2는 정확하게 같은 의미이다. 바이트코드를 보면 알 수 있다.

A.java
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FblcbRq%2FbtqRTSjUj98%2FRK3cjRaJ0ONTz4MMKW5310%2Fimg.jpg)

<br>

B.java
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbj8cPh%2FbtqRVpPiewP%2Fddv020MUkJpjP3eaGr9K9K%2Fimg.jpg)

<br>

A.java와 B.java를 컴파일해서 코드를 까 보면 정확히 같다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3PLEq%2FbtqRXH9Z5Jb%2FPESIguiIkzFdmHv61tYxIk%2Fimg.jpg)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdSOiPS%2FbtqR1D0xwMX%2FQY2iaQqhuYAJnHcrgM43qk%2Fimg.jpg)

<br>

빨간색으로 강조 표시를 한 곳이 메인 메서드의 바이트코드이다. `iconst_3` 은 정수 3을 호출 스택에 올린다는 뜻이고 `istore_1`은 정수를 첫 번째 변수에 저장한다는 뜻이다. 
이걸 보면 int a = 3; 으로 **선언과 초기화를 한 번에 해도 내부적으로는 두 번으로 나눠서 일을 처리한다는 것을 알 수 있다.** (멀티 쓰레드 환경에서 데이터 일관성이 깨지는 포인트가 여기다.)

# 변수의 스코프와 라이프타임

## Block Scope

변수 영역(scope)은 변수가 유효성을 갖는 범위를 말한다. 
C, C++, Java 등 현대의 대부분의 언어는 블록 스코프(Block scope)를 지원한다. 
블록 스코프는 변수가 블록( { ... } ) 안에서만 유효하며 블록의 바깥에서는 접근할 수 없는 특징을 가지고 있다.

```java
public static void main(String[] args) {    
    {
        int a = 10;
        System.out.println(a); // 10
    }
    
    System.out.println(a);  // 이 문장은 컴파일 에러를 발생시킨다.
}
```

## 멤버 변수와 지역 변수

Java에서 변수는 멤버 변수(member variables)와 지역 변수(local variables)로 나눠서 살펴볼 수 있다. 
멤버 변수는 반드시 클래스 내(단 메서드 밖)에서 선언되어야 하며, 클래스 내부 어디서든 접근이 가능하다.

```java
public class Variable {
    // 클래스 내부(단, 메서드 밖)에서 선언된 모든 변수는 멤버 변수이다.
    int a;
    private String b;
    void method1() {...}
    int method2() {...}
    char c;
}
```

멤버 변수는 접근 제어자에 따라 접근할 수 있는 범위를 제한할 수 있다.

|접근제어자|Package|Subclass|World|
|---|---|---|---|
|public   |가능   |가능   |가능   |
|protected   |가능   |가능   |불가능   |
|default   |가능   |불가능   |불가능   |
|private   |불가능   |불가능   |불가능   |

<br>

메서드 내에서 선언된 변수는 지역 변수라고 부르며 메서드 외부에서 접근할 수 없다.
```java
public class Variable {
    int a = 3;    //멤버 변수

    void method1() {
        int a = 5;    //지역 변수
    }
}
```

지역 변수는 멤버 변수와 이름이 같아도 문제가 되지 않는다. **둘은 이름만 같을 뿐 서로 다른 메모리에서 관리되는 서로 다른 변수이다.**
멤버 변수 a는 JVM의 힙 영역에서 관리되고, 지역 변수 a는 메서드가 호출될 때, 호출 스택에서 생성된 후 메서드가 종료되면 사라진다.

이름이 같기 때문에, 메서드 안에서 같이 사용하려면 this 예약어를 사용한다.

```java
public class Variable {
    int a = 3;

    void method1() {
        int a = 5;
        System.out.println(this.a);    // 3
        System.out.println(a);        // 5
    }
}
```

## 반복문 안에서의 변수

반복문 안에서 선언된 변수도 마찬가지로 반복문 블록 안에서만 유효하다.

```java
public class Variable {
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            System.out.println(i);
        }
        // 아래 코드는 컴파일 오류를 발생시킨다.
        System.out.println(i);
    }
}
```

위 코드는 아래와 같이 수정해야 한다.
```java
public class Variable {
    public static void main(String[] args) {
    
        int i;
        for (i = 0; i < 10; i++) {
            System.out.println(i);
        }
    
        System.out.println(i); // 10
    }
}
```

<br>

한 가지 주의할 점은 Java에서는 반복문 안의 변수와 바깥의 변수가 이름이 달라야 한다는 것이다.

```java{4,6}
public class Variable {
    public static void main(String[] args) {
    
        int i = 3;
        // 아래 코드는 컴파일 에러를 발생시킨다.
        for (int i = 0; i < 10; i++) {
            System.out.println(i);
        }
    }
}
```
같은 형태의 C++ 코드에서는 에러가 발생하지 않지만, Java에서는 루프 내부와 외부의 변수의 이름을 다르게 만들도록 강제하고 있다.

막간의 퀴즈로 다음과 같은 코드는 어떤 결과를 반환할까?

```java{4,7}
public class Variable {
    public static void main(String[] args) {
    
        for (int i = 0; i < 3; i++) {
            System.out.println(i);
        }
        int i = 3;
        System.out.println(i);
    }
}
```
Output

    0
    1
    2
    3

문제없이 잘 돌아간다. 순서만 바뀌었는데 왜 이번에는 에러가 발생하지 않을까? for loop가 종료되는 시점, 즉 " } " 부분에서 for loop 내에서 선언된 int i 는 호출 스택에서 사라진다.
그렇기 때문에 for loop가 종료되고 난 후에는 int i를 새로 만들어서 사용할 수 있는 것이다.

## Lifetime

변수의 라이프타임(생명주기)은 변수가 메모리에 생성되고 사라지기까지의 시간을 일컫는다. 
이는 앞에서 살펴본 변수의 스코프와도 관련된다.

우선 앞에 static 예약어가 붙는 `클래스 변수`는 **클래스가 메모리에 로드되는 시점에 생성**된다. 
클래스는 해당 클래스가 프로그램에서 처음 사용될 때 Runtime Data Area의 Method Area에 생성되며 프로그램이 종료될 때까지 계속 메모리에 남아있게 된다.

`인스턴스 변수`는 **해당 인스턴스가 메모리에 남아있는 시간동안 존재**한다. 인스턴스는 힙 영역에 생성된다. 인스턴스에 대한 참조가 사라지면 가비지 컬렉터에 의해 삭제된다.

`지역 변수`는 **변수가 포함된 메서드가 호출될 때 호출 스택에 생성되며 해당 메서드가 종료되면 함께 사라진다.**


# 타입 변환, 캐스팅 그리고 타입 프로모션

프리미티브 타입 8가지 중 boolean을 제외한 나머지 타입들은 서로 타입 변환이 가능하다. 
이를 `타입 변환` 혹은 `형변환(Type Conversion)`이라고 한다. 프로그래밍을 하다보면 서로 다른 자료형끼리 연산을 해야될 일이 생길 때가 있다(char 자료형 변수와 int 자료형 변수를 더해야 한다든지).
이때 필요한 것이 형 변환이다. 형변환에는 `타입 프로모션`과 `캐스팅` 두 가지가 있다.

## 타입 프로모션(Type promotion)

타입 프로모션(Type Promotion)은 표현 범위가 더 넓은 자료형으로 변환되는 것을 말한다.
그래서 Widening Conversion이라고도 한다. 이는 컴파일러가 알아서 해주기 때문에 자동 형변환이라고도 한다(이름이 참 많다;). 예시를 살펴보자.

```java
public static void main(String[] args) {

    int a = 5;
    long b = 10L;
    
    // 더하기 전에 컴파일러가 미리 int를 long으로 변환
    long c = a + b;
    
    System.out.println(c);  // 15
}
```

이렇게 프로그래머가 명시적으로 형변환을 하는 작업을 해주지 않아도 컴파일러가 알아서 변환을 해준다.
타입 프로모션은 **반드시 표현 범위가 좁은 자료형에서 넓은 자료형으로 일어난다.** 
만약 반대의 경우에도 컴파일러가 마음대로 형변환을 해버린다면? 
표현 범위가 좁아지기 때문에 프로그래머가 의도치 않은 값 손실이 일어날 수 있다.

타입 프로모션에는 **몇가지 규칙**이 있다.

1. byte와 short는 무조건 int로 변환된다.
2. 만약 피연산자들 중 하나가 long 타입이라면, 최종 값은 long으로 변환된다.
3. 만약 피연산자들 중 하나가 float 타입이라면, 최종 값은 flaot으로 변환된다.
4. 만약 피연산자들 중 하나가 double 타입이라면, 최종 값은 double로 변환된다.

double은 원시 타입 중에서 표현범위가 가장 넓기 때문에 피연산자들 중 하나라도 double 타입이면 무조건 double로 변환된다. 최종보스같은 느낌

예를 들어
```java
long a = 10L;
double b = 5.0;
```

두 변수 a와 b를 더하면 결과값은 double 자료형이 된다.

1번 규칙에 대해 약간 설명을 덧붙이면, 자바 바이트코드 opcode에는 byte와 short를 스택 메모리에 적재하는 명령어가 없기 때문에 byte와 short는 무조건 int로 메모리에 올라가게 된다. 무슨 말이냐면

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrTXNp%2FbtqSbaXEPph%2FlSKbABa6UJHRTTQJrajzu1%2Fimg.jpg)

이렇게 코드를 작성하고 컴파일 해보면

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbKYJbM%2FbtqSjz97BZS%2FQBnNAbvYqRIgisRGBkEjIK%2Fimg.jpg)

이런 바이트코드를 얻을 수 있는데, 0번과 3번 라인의 bipush 명령어는 byte 자료형을 int 자료형으로 스택에 푸시하는 명령어이다. 스택에 애초에 int로 올라가게 된다는 말이다.

<br>

그래서 재밌게도 다음과 같은 코드는 컴파일 오류가 난다.
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcP0B9z%2FbtqSdtiizFz%2FVXrcf0WWgHoko6IxYps8i0%2Fimg.jpg)

컴파일 오류가 안나게 하려면 변수 c의 자료형을 int로 선언해주거나, a+b를 byte로 캐스팅 해주어야 한다.

## 캐스팅(Casting)

캐스팅은 **표현 범위가 더 넓은 자료형애서 좁은 자료형으로 변환하는 것**이다. 그래서 Narrow Conversion이라고도 한다. 

캐스팅은 캐스팅할 변수 혹은 리터럴 앞에 (타겟 자료형) 을 붙여주면 된다.

```java
public static void main(String[] args) {
    int a = (int) 10.5;
    System.out.println(a);    // 10
}
```

캐스팅은 표현 범위가 줄어들기 때문에 위 코드처럼 10.5 -> 10으로 값 손실이 일어난다. int 자료형은 소수점을 표현할 수 없기 때문에 뒷부분을 날려버리고 저장하는 것이다. 
값의 손실이 일어나기 때문에 컴파일러가 알아서 해주지 않고 프로그래머가 필요에 따라서 명시적으로 지정해주어야한다. 그래서 캐스팅을 **강제 형변환**이라고 부르기도 한다.

# 타입 추론 var

타입 추론은 프로그래머가 직접 타입을 명시하지 않고, 컴파일러가 알아서 타입을 찾아내는 것을 말한다. 
자바는 소스코드 작성 시에 타입을 반드시 지정해주어야 하는 강타입의 언어이다. 그런데 편의와 가독성을 위해 Java 10부터 타입 추론을 지원하는 var가 도입되었다.
var를 사용하면 타입을 명시하지 않아도 된다. 단 **var 사용 시에는 반드시 선언과 초기화를 동시에 해줘야한다.**

```java
String message = "Hello World";    // 기존
var message = "Hello World";    //var 사용
```

[var를 제대로 활용하는 방법](https://dev.to/composite/java-10-var-3o67)에 대해 정리한 글을 소개한다.

# 1차, 2차원 배열 선언하기

## 1차원 배열

배열은 같은 타입의 자료들을 하나의 이름으로 묶어놓은 것이다.

자바의 배열은 다음과 같은 특징을 가지고 있다.
* 자바의 모든 배열은 동적으로 할당된다.
* 배열은 Object를 직접 상속받고 Object의 property인 length를 사용해 길이를 얻을 수 있다.
* 자료형 뒤에 []를 붙여 선언한다.
* 배열 내의 원소들은 순서를 갖고 있으며, index는 0부터 시작한다.
* 배열의 크기는 int로 정해지며, long이나 short를 사용할 수 없다.
* 모든 배열은 Cloneable과 java.io.Serializable을 구현한다.

<br>

배열은 프리미티브 타입이나 객체를 요소로 가질 수 있다. 일반적인 형태는 1차원 배열이며 다음과 같이 선언한다.

    type[] var-name
    type var-name[]


첫번째는 자바 스타일의 선언 방법이고, 두번째는 C 스타일의 선언 방법이다. 왠만하면 자바 스타일을 따르도록 하자.

선언은 단지 컴파일러에게 이 배열이 어떤 타입의 데이터를 가지고 있을 것인지 알려주는 역할만 할 뿐 실제로 메모리를 생성하지 않는다. 
**실제로 메모리를 할당 받으려면 new 연산자와 함께 배열의 크기를 명시해주어야 한다.** 예를 들어 크기가 5인 정수 배열을 생성하려면 다음과 같이 작성해야한다.
```java
int[] intArr = new int[5];
```

배열을 생성하면, 배열은 자동적으로 배열이 가지는 타입의 기본값으로 초기화한다. 
**boolean은 false, 객체는 null 그리고 나머지는 모두 0으로 초기화된다.**

배열을 선언하면서 다음과 같이 동시에 값을 초기화해 줄 수도 있다.

```java
int[] intArr = new int[] {1, 2, 3, 4, 5};
```

모던 자바에서는 new를 생략할 수 있다.
```java
int[] intArr = {1, 2, 3, 4, 5};
```

위 문장은 아래와 완전히 동일한 의미를 갖는다

```java
int[] intArr = new int[5];
intArr[0] = 1;
intArr[1] = 2;
intArr[2] = 3;
intArr[3] = 4;
intArr[4] = 5;
```

배열은 인덱스로 바로 접근이 가능하다. 배열은 연속된 메모리 공간에 존재하기 때문에 배열의 시작 주소와 인덱스만 알면 O(1)의 시간 복잡도로 접근이 가능하다.
```java
int secondNumber = intArr[1];
```

만일 배열의 범위를 벗어난 곳에 접근하려고 하면 **ArrayIndexOutOfBoundsException**을 일으킨다.

## 2차원 배열

2차원 배열은 배열을 요소로 갖는 배열로 이해하면 된다.
```java
int[][] twoDimensionArr = new int[10][20]
```

다음은 3 x 3의 이차원 배열을 생성하고 출력하는 예제이다.
```java
public static void main(String args[]) {
    
    // declaring and initializing 2D array
    int arr[][] = { {2,7,9},{3,6,1},{7,4,2} };
    
    // printing 2D array 
    for (int i=0; i< 3 ; i++) { 
        for (int j=0; j < 3 ; j++) { 
            System.out.print(arr[i][j] + " ");   
            System.out.println(); 
        } 
    } 
}
```
Output

    2 7 9
    3 6 1
    7 4 2
 

