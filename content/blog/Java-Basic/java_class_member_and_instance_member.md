---
title: '[Java] 클래스 멤버는 왜 인스턴스 멤버를 참조할 수 없을까?'
date: 2020-12-30 14:23:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

# 클래스 멤버와 인스턴스 멤버 간 참조 관계

클래스 멤버(스태틱 변수, 스태틱 메서드)는 인스턴스 멤버를 사용할 수 없다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrH8Dt%2FbtqRI79aIgS%2FfcXweqb1yBvehVrDCGKxp1%2Fimg.jpg)

위 스크린샷은 인텔리제이에서 해당 코드를 작성했을 때의 화면인데, static 예약어가 사용된 클래스 멤버에서 인스턴스 메서드를 호출하거나 인스턴스 변수를 사용하려고하면 빨간줄이 그어지면서 컴파일 에러가 발생한다.

반면 인스턴스 메서드에서 클래스 변수를 사용하거나 클래스 메서드를 호출하는데는 아무런 문제가 없다.

왜 이런 일이 발생할까? 이는 **클래스 멤버와 인스턴스 멤버의 생명주기**와 관련이 있다.

# JVM의 메모리 구조

우선 JVM의 메모리 구조를 살펴보자. JVM의 Runtime Data Area는 서로 다른 용도를 가진 영역으로 구분되어 있다.

그 중 메서드 영역, 호출 스택, 힙 영역을 살펴보자.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F5Ba1Y%2FbtqRGqVvL3q%2FhYVjlr4J96pKkxYcpcUhi0%2Fimg.png)

* 메서드 영역(method area)
    - 프로그램 실행 중에 어떤 클래스를 만나게 되면, JVM은 해당 클래스의 바이트코드를 읽어서 클래스에 대한 정보를 이곳에 저장한다. 이 때, 그 클래스의 클래스 변수도 이 영역에 함께 생성된다.
* 힙(heap)
    - 인스턴스가 생성되는 공간이다. 인스턴스가 생성될 때, 인스턴스에 대한 정보들은 이곳에 저장된다.
* 호출스택(call stack or execution stack)
    - 호출스택은 메서드가 호출될 때, 메서드의 실행에 필요한 데이터를 위해 메모리 공간이 할당된다. 이 메모리는 메서드가 실행되는 동안 지역변수들과 연산의 중간 결과 등을 저장하는데 사용된다. 메서드가 작업을 마치면 할당되었던 메모리 공간은 반환되어 비워진다. 
      
<br>

클래스 멤버는 클래스가 메모리에 로딩될 때 생성된다. 인스턴스 멤버는 인스턴스가 생성될 때 메모리에 로딩되고 생성된다. 즉 이 둘은 메모리에 생성되는 시기와 장소가 다르다.

<br>

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbRhqvw%2FbtqRL4j6i3q%2FiKUBmDkZUKwGdyXF7Q5KTK%2Fimg.jpg)

<br>

근데 잘 생각해보면, 인스턴스가 생성되는 시점에는 항상 클래스가 메모리에 로딩되어 있음이 보장된다. 클래스가 존재하지 않는데 그 클래스의 인스턴스를 생성할 수는 없기 때문이다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fl5a7Q%2FbtqRTRYumpn%2FelKNbtSydkBdBDhKABbP70%2Fimg.jpg)
(이 시점에 클래스도 로드되고 인스턴스도 생성됨)

<br>

하지만 클래스가 로드되는 시점에 인스턴스가 없는 경우는 충분히 존재할 수 있다.
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbWIe9s%2FbtqRTSb1xCJ%2FAwPRI9KS8KbKn8mhMhjEq0%2Fimg.jpg)
(인스턴스는 필요없음)

그러니 인스턴스 멤버는 클래스 멤버를 사용해도 아무런 문제가 없다. 이미 클래스는 존재하기 때문!!

하지만 클래스 멤버는 인스턴스 멤버를 사용할 수 없다. 인스턴스가 존재하지 않을 수도 있기 때문이다.







