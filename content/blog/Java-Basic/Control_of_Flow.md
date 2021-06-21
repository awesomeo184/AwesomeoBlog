---
title: '[Java Study 4주차] 자바 제어문'
date: 2021-1-2 13:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

<br>

* 목표: 자바가 제공하는 제어문을 학습하세요.

# 제어문

프로그램의 실행 제어에는 세 가지 구조가 있다.

![699451EF-0808-4A1D-81F2-C2643711179C](https://user-images.githubusercontent.com/63030569/115996744-89bb1880-a61b-11eb-975c-0c950e5b9bdc.png)

순차 구조에서 벗어나 프로그래머가 원하는 실행 흐름대로
프로그램을 제어하기 위한 문장(statement)을 제어문이라고 한다. 그 중 선택 구조와 반복 구조를 위해 사용되는 문장을 각각 선택문, 제어문이라고 한다.

## 선택문

자바의 선택문에는 `if-else` 예약어를 사용하는 `if문`과 `switch-case` 예약어를 사용하는 `switch문`이 있다.

### if 문

가장 간단한 형태의 선택문이다. 조건이 참이면 블록 안의 코드를 수행하고 거짓이면 수행하지 않는다.

```java
if (condition) {
    참이라면 여기 문장들이 실행된다.
}
```

```java
class IfDemo { 
    public static void main(String args[]) { 
        int i = 10; 
  
        if (i > 15) 
            System.out.println("10은 15보다 작다"); 
   
        System.out.println("여긴 if문 밖"); 
    } 
} 
```


만약 블록이 없다면 조건이 참일 때 if문 다음의 한 문장을 실행한다. 예를 들어 아래 코드에서 condition이 참일 때만 statement1이 실행된다.
statement2는 if문과 상관없으므로 항상 실행된다.

```java
if (condition)
statement1;
statement2;
```

> 단 이때는, statement1에서 새로운 변수를 선언할 수 없다. 변수 선언은 반드시 블록 내에서만 수행되기 때문이다([공식문서 참고](https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.4)). 

실행할 문장이 하나일 때, 이처럼 중괄호를 생략해도 되지만 [구글 자바 코딩 스타일 가이드](https://google.github.io/styleguide/javaguide.html#s4.1-braces)에서는 
이런 경우에도 중괄호를 생략하지 않기를 권장하고 있다.

### if-else문

if문은 단순히 조건이 참이면 블록의 내용을 실행하고 거짓이면 실행하지 않는다. 하지만 만약 조건이 거짓일 때도 무언가 실행하도록 하고 싶다면, else 예약어를 이용하면 된다.

```java
if (condition) {
    조건이 참이면 실행
} else {
    조건이 거짓이면 실행
}
```

```java
class IfElseDemo { 
    public static void main(String args[]) { 
        int i = 10; 
  
        if (i < 15) 
            System.out.println("i는 15보다 작다."); 
        else
            System.out.println("i는 15보다 크다."); 
    } 
} 
```

### 중첩 if문(nested if-statement)

만약 어떤 조건이 참일 때, 또 다시 다른 조건에 따라 흐름을 제어하고 싶다면 중첩 if문을 이용하면 된다.

```java
if (condition1) { 
    // condition1이 참이면 실행된다. 
    if (condition2) { 
        // condition2가 참이면 실행된다.
        // 즉 이 부분이 실행되려면 condition1, 2가 모두 참이여야 함.
    } 
}
```

```java
class NestedIfDemo { 
    public static void main(String[] args) {
        int i = 17;
        if (i < 20) {
            System.out.println("i는 20보다 작다");
            if (i < 15) {
                System.out.println("i는 15보다 작다");
            } else {
                System.out.println("i는 15보다 크다");
            }
        }
    }
} 
```

### if-else-if

만약 판단해야 할 조건이 여러개라면 if-else-if를 이용해볼 수 있다. 첫 번째 조건이 거짓이라면 다음 조건을 판단한다. 만일 첫번째 조건이 참이면 나머지 문장은 수행되지 않는다.

```java
class IfElseIfDemo { 
    public static void main(String args[]) { 
        int i = 15; 
  
        if (i == 10) 
            System.out.println("i 는 10"); 
        else if (i == 15) 
            System.out.println("i 는 15"); 
        else if (i == 20) 
            System.out.println("i 는 20"); 
        else
            System.out.println("i 는 앞의 조건들을 하나도 만족하지 못함"); 
    } 
} 
```

### switch-case문

switch-case문은 여러 방향으로 분기할 수 있는 문이다.

```java
switch (expression) { 
	
    case value1: 
    	statement1; 
        break; 
    
    case value2: 
    	statement2; 
        break; 
    
    . . 
    
    case valueN: 
    	statementN; 
        break; 
        
    default: defaultStatement; 
}
```

괄호 부분에는 값으로 평가될 수 있는 식이 들어가야 한다. expression과 value가 일치하는 경우, 해당 case에 있는 코드가 실행된다.

expression 부분에는 byte, short, char, int 그리고 열거형이 들어갈 수 있다. Java 7 부터는 String도 들어갈 수 있다. 
value 부분에는 중복된 값이 들어갈 수 없다. default는 일치하는 value가 없는 경우 실행된다. break는 일치하는 case의 코드를 실행한 뒤 바로 switch문을 탈출할 수 있게 해준다.

defualt와 break는 생략될 수 있다. 다만 default가 생략되는 경우에는, 일치하는 value가 없는 경우 아무것도 실행되지 않는다. break가 생략되는 경우, 일치하는 case의 블록이 실행된 후 나머지 case도 모두 실행된다. 예시를 통해 알아보자.

```java
class SwitchCaseDemo { 
    public static void main(String args[]) { 
        int i = 1; 
        switch (i) 
        { 
        case 0: 
            System.out.println("i 는 0 입니다.."); 
            break; 
        case 1: 
            System.out.println("i 는 1 입니다."); 
            break; 
        case 2: 
            System.out.println("i 는 2 입니다."); 
            break; 
        default: 
            System.out.println("i 는 2 보다 큽니다."); 
        } 
    } 
} 
```

<br>

출력 결과
```
i 는 1 입니다.
```

<br>

break를 생략하는 경우

```java
class SwitchCaseDemo { 
    public static void main(String args[]) { 
        int i = 1; 
        switch (i) 
        { 
        case 0: 
            System.out.println("i 는 0 입니다.."); 
        case 1: 
            System.out.println("i 는 1 입니다."); 
        case 2: 
            System.out.println("i 는 2 입니다."); 
        default: 
            System.out.println("i 는 2 보다 큽니다."); 
        } 
    } 
} 
```
<br>

출력 결과
```
i 는 1 입니다.

i 는 2 입니다.

i 는 2 보다 큽니다.
```

매 케이스마다 break를 넣어줘야 하는 것은 참 불편한 일이다. 이외에도 switch문은 사용하기 불편한 부분이 몇몇 있었는데, 

Java 12에서 switch-expression이 도입되면서 이전보다 편하게 switch-case를 사용할 수 있게 되었다.

switch-expression에 대한 내용은 [3주차 스터디](https://awesomeo184.netlify.app/Java-Basic/Operator/#switch-%EC%97%B0%EC%82%B0%EC%9E%90)에 정리해두었다.

## 반복문

### while-loop

while loop는 조건을 검사하는 것으로 시작한다. 조건이 참이면 loop body를 실행하고 거짓이면 while loop 바로 뒤에 이어지는 statement를 실행한다.
```java
while (boolean condition) {
    statement;
}
```

![](https://media.geeksforgeeks.org/wp-content/uploads/Loop1.png)
출처: https://www.geeksforgeeks.org/loops-in-java/?ref=lbp

Loop Body에서는 다음 반복을 위한 변수를 업데이트 해주는 것이 일반적이다. 다음 예시의 변수 x가 이에 해당한다.

```java
class WhileLoopDemo { 
    public static void main(String args[]) { 
        int x = 1; 
  
        while (x <= 4) 
        { 
            System.out.println(x); 
            x++; 
        } 
    } 
}
```

출력 결과
```
1
2
3
4
```

### for-loop

while-loop은 괄호 안에 condition밖에 못들어가지만 for-loop는 initialization, condition, increment/decrement를 사용해 좀 더 편하게 반복을 제어할 수 있다.

for-loop은 변수 초기화 이후, 조건 검사 -> 블록 실행 -> increment/decrement -> 조건 검사 -> ... 순서로 반복된다.

~[](https://media.geeksforgeeks.org/wp-content/uploads/loop2.png)
출처: https://www.geeksforgeeks.org/loops-in-java/?ref=lbp

```java
class ForLoopDemo { 
    public static void main(String args[]) { 
        for (int x = 1; x < 4; x++) 
            System.out.println(x); 
    } 
} 
```

<br>

출력 결과
```
1
2
3
```

<br>

### Enhanced-for-loop

Java 5에 추가된 기능이다. 시퀀스 객체를 인덱스 없이 순회할 때 사용한다. 이때 객체와 변수는 immutable 해야 한다. 반복이 수행되는 동안 객체나 변수가 update 되어서는 안된다.

```
for (T element:Collection obj/array) {
        statement(s)
}
```

```java
public class EnhancedForLoop { 
    public static void main(String args[]) { 
        String array[] = {"A", "B", "C"}; 
  
        // enhanced for loop 
        for (String x : array) { 
            System.out.println(x); 
        } 
  
        // 같은 기능의 for loop
        for (int i = 0; i < array.length; i++) { 
            System.out.println(array[i]); 
        } 
        
    } 
} 
```

### do-while loop

while문은 조건을 먼저 검사하고 블록을 수행하지만, do-while문은 조건이 참이든 거짓이든 무조건 블록을 한 번 수행하고 나서 조건을 검사한다.

```java
class DoWhileLoopDemo 
{ 
    public static void main(String args[]) { 
        int x = 21; 
        do { 
            System.out.println("Value of x:" + x); 
            x++; 
        } 
        while (x < 20); 
    } 
}
```

### 반복문 사용시 주의할 점

루프를 도는 동안 collection 객체에 무언가를 더하면 OutOfMemoryError가 발생할 수도 있으므로 조심해야 한다.

```java
ArrayList<Integer> arr = new ArrayList<>(); 
for (int i = 0; i < Integer.MAX_VALUE; i++) { 
    arr.add(i); 
} 
```


### 참고 자료

https://www.geeksforgeeks.org/decision-making-javaif-else-switch-break-continue-jump/?ref=lbp#if

https://www.geeksforgeeks.org/loops-in-java/?ref=lbp