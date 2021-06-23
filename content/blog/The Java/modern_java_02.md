---
title: '[모던 자바 인 액션] 동작 파라미터화'
date: 2021-06-23 14:23:27
category: 'The Java'
thumbnail: { thumbnailSrc }
draft: false
---

> 본 포스팅은 [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987?pid=123487&cosemkid=go15646485055614872&gclid=Cj0KCQjwlMaGBhD3ARIsAPvWd6hlrig9OO8CPbXuTVjEpHkYK5fNbBxY79oYw4P4jyINdLLaf8tZU2IaAkTEEALw_wcB)
> 을 읽고 내용을 제 나름대로 정리한 것입니다. 자세한 내용은 반드시 해당 책에서 확인하시길 바랍니다. 

객체 지향 패러다임이 각광을 받는 가장 큰 이유는 변화에 대한 대응이다. 요구 사항 변화에 유연하게 대응하도록
설계하는 것은 소프트웨어 유지 보수에 있어 매우 중요하다.

`동작 파라미터화`를 이용하면 자주 바뀌는 요구사항에 대해 효과적으로 대응할 수 있다.

이번 포스팅에서는 동작 파라미터화가 어떻게 변화에 유연하게 대응할 수 있는지를 살펴보고 Java 8의 람다 표현식이
동작 파라미터화 코드를 간단하게 표현할 수 있음을 확인한다. 모던 자바 인 액션 2장의 내용을 참고하였다.

동작 파라미터화는 메서드의 동작을 파라미터화해서 코드 블룩의 실행을 나중으로 미루는 것을 말한다.

# 예제) 사과 필터링

사과들이 담긴 인벤토리에서 녹색 사과만 필터링하는 코드를 작성해보자

```java
enum Color {RED, GREEN}
```

```java{5}
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    
    for (Apple apple: inventory) {
        if (GREEN.equals(apple.getColor()) {
            result.add(apple);
        }
    }
    
    return result;
}
```

만약 요구 사항이 변경되어 이번에는 빨간색 사과만 필터링하고 싶다고 해보자. 가장 단순하게는 위 코드에서 표시한 부분을 빨간색으로 바꾼 filterRedApple() 메서드를 하나 더 만들면 된다.

하지만 옅은 녹색, 노란색 등등 요구 사항이 계속 추가된다면 조건문 하나만 바뀐 비슷한 코드가 반복해서 나타날 것이다. 이때는 다음과 같은 규칙을 적용할 수 있다.

> 거의 비슷한 코드가 반복 존재한다면 그 코드를 추상화한다.

## 색을 파라미터화

다음과 같이 색을 파라미터화 한다면 메서드 하나로 색깔에 대한 요구사항을 모두 반영할 수 있다.

```java{5}
public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
    List<Apple> result = new ArrayList<>();
    
    for (Apple apple: inventory) {
        if (apple.getColor().equals(color)) {
            result.add(apple);
        }
    }
    
    return result;
}
```

## 무게를 파라미터화

그런데 이번엔 색깔이 아니라 무게에 따라 사과를 분류해달라는 요청이 왔다. 이에 대해 다음과 같은 코드를 작성할 수 있다.

```java{5}
public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) {
    List<Apple> result = new ArrayList<>();
    
    for (Apple apple: inventory) {
        if (apple.getWeight() > weight) {
            result.add(apple);
        }
    }
    
    return result;
}
```

또 코드가 중복되어 나타난다. 이번에는 중복되는 부분을 어떻게 추상화할 수 있을까?

## 가능한 모든 속성으로 필터링

두 메서드를 합쳐버릴 수 있다. 그러면 사과를 색깔로 필터링할지 무게로 필터링할지를 나타내는 플래그 변수를 하나 더 사용해야 한다. 다음과 같이 코드를 작성할 수 있다.

```java
public static List<Apple> filterApple(List<Apple> inventory, Color color, 
                                                int weight, boolean flag) {
    List<Apple> result = new ArrayList<>();
    
    if ((flag && apple.getColor(color)) || (!flag && apple.getWeight() > weight) {
        result.add(apple);
    }
    
    return result;
}
```

위 메서드는 다음과 같이 사용한다

```java
List<Apple> greenApples = filterApples(inventory, GREEN, 0, true);
List<Apple> heavyApples = filterApples(inventory, null, 150, false);
```

<br>

결론부터 말하자면 이는 매우 안좋은 코드이다.

우선 클라이언트 입장에서 사용하기가 매우 불편하다. boolean 인자에 true가 무엇을 의미하는지, false가 무엇을 의미하는지 알 수가 없기 때문이다.
또 요구사항의 변화에도 유연하게 대응할 수 없다. 예를 들어 녹색 사과 중에서 무거운 사과를 필터링하고 싶다거나, 분류 조건을 추가하고 싶은 경우에 전체 코드를 수정해야한다.

## 동작 파라미터화

우리는 동작 파라미터화를 통해 변경에 유연한 코드를 만들 수 있다. 일단 선택 조건을 다음처럼 결정할 수 있다. 사과의 어떤 속성에 기초해 불리언값을 반환한다.

선택 조건을 결정하는 인터페이스(Predicate)를 정의한다.

```java
public interface ApplePredicate {
    boolean test(Apple apple);
}
```

<br>

그리고 다양한 선택 조건을 대표하는 여러 버전의 ApplePredicate를 정의할 수 있다.

```java
public class AppleHeavyWeightPredicate implememts ApplePredicate {
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight() > 150;
    }
}
```
```java
public class AppleGreenColorPredicate implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
        return GREEN.equals(apple.getColor());
    }
}
```

<br>

이제 filterApples() 메서드에서 ApplePredicate 객체를 받아 애플의 조건을 검사하도록 메서드를 고친다. 이렇게 **동작을 파라미터화**, 즉 
메서드가 다양한 동작을 받아서 내부적으로 다양한 동작을 수행할 수 있다.

```java
public static List<Apple> filterApples(List<Apple> inventory,
                                                    ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple: inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    
    return result;
} 
```
<br>

이제 필요한대로 다양한 ApplePredicate을 만들어서 filterApples() 메서드로 전달할 수 있다. 예를 들어 "150그램이 넘는 빨간 사과만 검색하라"라는 요구 조건이
들어오면 다음과 같은 ApplePredicate 객체를 작성해서 filterApples() 메서드에 전달하기만 하면 된다.

```java
public class AppleRedAndHeavyPredicate implements ApplePredicate {
    public boolean test(Apple apple) {
        return RED.equals(apple.getColor()) && apple.getWeight() > 150;
    }
}
```

```java
List<Apple> redAndHeavyApples = filterApples(inventory, new AppleRedAndHeavyPredicate());
```

그림으로 나타내면 동작이 인자로 전달된다는 것을 명확히 알 수 있다.

![KakaoTalk_Photo_2021-06-23-15-31-57](https://user-images.githubusercontent.com/63030569/123047356-2ccfb900-d438-11eb-946b-5386c4327502.jpeg)

이처럼 동작 파라미터화는 Java 8에 새로 추가된 기능들을 이용하지 않고도 동작이 가능하다. 그러나 아쉬운 점은 우리에게 필요한건 test()라는 메서드인데, 
메서드는 객체만 인수로 받을 수 있으므로 이를 전달하기 위해 test()를 ApplePredicate 객체로 감싸서 전달해야한다는 것이다.

Java 8에 도입된 람다를 이용하면 여러 개의 ApplePredicate 클래스를 정의하지 않고도 표현식을 통해 메서드를 전달할 수 있다.

```java
List<Apple> result = filterApples(inventory, 
                apple -> RED.equals(apple.getColor()) && apple.getWeight() > 150);
```