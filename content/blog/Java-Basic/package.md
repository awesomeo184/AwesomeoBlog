---
title: '[Java Study 7주차] 패키지'
date: 2021-01-09 15:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

<br>

* 목표: 자바의 패키지에 대해 학습하세요.

<br>

공부할 것

* package 키워드
* import 키워드
* 클래스패스
* CLASSPATH 환경변수
* -classpath 옵션
* 접근지시자

# package keyword

## package란?

패키지는 클래스의 묶음이다. 패키지에는 클래스와 인터페이스가 포함될 수 있으며, 서로 관련된 패키지들끼리 그룹 단위로 묶어 놓음으로써 클래스를 효율적으로 관리할 수 있다.

패키지의 물리적인 형태는 파일 시스템의 디렉토리이다. 같은 이름을 가진 클래스라도 속한 패키지가 다르면 구분될 수 있으므로 이름의 충돌을 피할 수 있다.

디렉토리가 하위 디렉토리를 가질 수 있는 것처럼 패키지도 하위 패키지를 가질 수 있으며 도트'  . '로 구분한다.

패키지 이름은 클래스와 구분하기 위해 보통 소문자로 적는다.


## FQCN (Fully Qualified Class Name)

모든 클래스에는 정의된 클래스 이름과 패키지 이름이 있다.

이 둘을 합쳐야 완전하게 한 클래스를 표현할 수 있으며 FQCN(Fully Qualified Class Name)이라 한다.

ex) String 클래스의 패키지는 "java.lang" 이며 FQCN은 "java.lang.String"이 된다.


## 패키지 선언

패키지는 다음과 같이 선언한다.

```java
package 패키지명;
```

패키지는 단 한번만 선언되어야 하며 소스코드 파일에서 주석과 공백을 제외한 가장 첫번째 줄에 와야한다.

소스파일에 자신이 속할 패키지를 지정하지 않은 클래스는 자동적으로 자바에서 제공하는 '이름 없는 패키지(unnamed package)'에 속하게 된다.

소스파일을 컴파일할 때, -d 옵션을 주게되면 소스파일에 지정된 경로를 통해 패키지의 위치를 찾아서 클래스파일을 생성한다. -d 옵션 뒤에는 해당 패키지의 루트 디렉토리의 경로를 적어준다. 만약 해당 경로에 디렉토리가 없으면 자동으로 생성한다.

```
$ javac -d [/루트디렉토리/경로] Test.java
```

# import keyword

소스코드를 작성할 때 다른 패키지의 클래스를 사용하려면 패키지명이 포함된 클래스 이름을 사용해야 한다. 하지만 import 키워드를 사용하면 클래스 이름에서 패키지명은 생략할 수 있다.

import 키워드의 역할은 컴파일러에게 소스파일에 사용된 클래스의 패키지에 대한 정보를 제공하는 것이다. 컴파일 시에 컴파일러는 import문을 통해 소스파일에 사용된 클래스들의 패키지를 알아낸 다음, 모든 클래스이름 앞에 패키지명을 붙여 준다.

모든 소스파일에서 import문은 package문 다음에, 클래스 선언문 이전에 와야 한다. package문과 달리 import문은 여러번 선언할 수 있다.

같은 패키지에 여러 클래스가 사용될 때 * 를 사용할 수 있다. 단 하위 패키지의 클래스까지 포함하는 것은 아니다.

```java
import java.util.*;
import java.text.*;
```

위 두 문장을 아래 한 문장으로 대체할 수 없다.

```java
import java.*;
```

## static import

static import문을 이용하면 static멤버를 호출할 때 클래스 이름을 생략할 수 있다.

```java
import static java.lang.Math.*;
import static java.lang.System.*;

public class StaticImport {

    public static void main(String[] args) {
        out.println(random());
    }
}
```

# CLASSPATH

클래스패스는 JVM이 자바 컴파일러에게 사용자 정의 클래스와 패키지의 위치를 지정해주는 파라미터이다. 
클래스로더가 메모리에 클래스 정보를 적재할 때, JVM이 클래스패스를 통해 클래스로더에게 클래스 파일의 경로를 알려주는 것이다.

### BootStrap Class Loader

기본 클래스로더 중 최상위 클래스 로더로써, jre/lib/rt.jar에 담긴 JDK 클래스 파일을 로딩 해준다. String 클래스나, Object 클래스를 사용할 수 있었던 이유가 바로, BootStrap Class Loader 가 자동으로 메모리에 적재해주기 때문이다.

### Extension Class Loader

익스텐션 클래스로더는 jre/lib/ext 폴더나 java.ext.dirs 환경 변수로 지정된 폴더에 있는 클래스 파일을 로딩한다.

### System Class Loader

바로 System Class Loader 가 우리가 만든 class 를 메모리에 올리는 역할을 하는데, 그 때, classpath 기준으로 클래스들을 로드해준다.

클래스패스를 지정하는데는, **환경 변수 CLASSPATH를 사용하는 방법**과 **Java runtime 에 -classpath 옵션을 사용하는 방법**이 있다.

## CLASSPATH 환경변수

환경변수는 운영체제에 지정하는 변수로 자바 가상머신과 같은 애플리케이션들은 환경변수의 값을 참고해서 동작하게 된다.

JVM이 시작될 때 JVM의 클래스 로더는 이 CLASSPATH 환경 변수를 호출한다. 그래서 환경 변수에 설정되어 있는 디렉토리가 호출되면 그 디렉토리에 있는 클래스들을 먼저 JVM에 로드한다. 그러므로 CLASSPATH 환경 변수에는 필수 클래스들이 위치한 디렉토리를 등록하도록 한다.

환경변수를 설정하는 자세한 방법은 [이 블로그](https://programmerchoo.tistory.com/212)를 참조하면 좋을듯 하다.

## -classpath 옵션

java 컴파일 명령(javac)과 자바 실행 명령(java)에서 -classpath 옵션을 사용할 수 있다. -cp로 줄여서 사용할 수도 있다.

-classpath 옵션은 자바를 실행할 때 사용할 클래스들의 위치를 가상머신에게 알려주는 역할을 한다.현재 디렉토리에서 하위 디렉토리 lib 에 있는 Hello.class 파일을 사용하려면 다음과 같이 할 수 있다.

```
$ java -classpath .:lib Hello
```

* `.` 은 현재 디렉토리를 의미한다.
* `:`은 경로와 경로를 구분하는 구분자 역할을 한다.
* `..` 은 현재 디렉토리의 상위 디렉토리를 말한다.

따라서 위의 경로를 해석하자면 **현재 디렉토리와 lib 디렉토리에 있는 Hello.class 파일을 찾는다** 라는 뜻이다.

# 접근제어자 (Access modifier)

접근 제어자는 멤버 또는 클래스에 사용되어, 해당하는 멤버 또는 클래스를 외부에서 접근하지 못하도록 제한하는 역할을 한다. 접근 제어자는 4가지가 있다.
클래스나 멤버변수, 메서드, 생성자에 접근 제어자가 지정되어있지 않으면 접근 제어자가 default이다.


|제어자 / 접근 범위|	같은 클래스|	같은 패키지|	자손클래스	전체|
|---|---|---|---|
|public|	O|	O|	O|	O|
|protected|	O|	O|	O|	X|
|default|	O|	O|	X|	X|
|private|	O|	X|	X|	X|


접근 범위가 넓은 쪽에서 좁은 쪽 순으로 나열하면 다음과 같다.

```
public > protected > default > private
```

접근 제어자를 사용하는 이유는 클래스 내부에 선언된 데이터를 보호하기 위해서이다. 
또 외부에서 접근할 필요가 없는 멤버들을 private로 지정해서 외부에 노출시키지 않음으로써 복잡성을 줄일 수 있다. 모두 객체 지향에서 캡슐화(encapsulation)에 해당하는 내용이다.

만약 메서드 하나를 수정하는데 이 메서드의 접근 범위가 public이라면 메서드를 변경한 후 오류가 없는지 테스트해야하는 범위가 넓다.
만약 private이라면 해당 클래스 하나만 살펴보면 된다. 따라서 접근 제어자를 적절히 선택해서 접근 범위를 최소화하도록 노력하는 것이 좋다.