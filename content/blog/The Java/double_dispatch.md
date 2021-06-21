---
title: '[Java] 더블 디스패치(double dispatch)'
date: 2021-01-08 15:13:27
category: 'The Java'
thumbnail: { thumbnailSrc }
draft: false
---

[6주차 스터디](https://awesomeo184.netlify.app/Java-Basic/inheritance/#%EB%A9%94%EC%84%9C%EB%93%9C-%EB%94%94%EC%8A%A4%ED%8C%A8%EC%B9%98method-dispatch)에서 메서드 디스패치에 대해 공부했다.

메서드 디스패치에는 동적 메서드 디스패치와 정적 메서드 디스패치가 있다. 그리고 메서드 디스패치를 활용한 더블 디스패치라는 기법이 있다.

더블 디스패치는 메서드 디스패치를 두 번 사용하여 유연성을 높이는 것인데, Visitor pattern에서 사용되는 기법이다.

예제는 토비님의 강의를 참고했다.

## 요구 사항 및 구현

자 우선 SNS 플랫폼에 맞게 포스팅을 뿌리는 서비스를 개발한다고 생각해보자.

* SNS라는 도메인과 Post라는 서비스가 있다.
* SNS의 구현체로는 현재 페이스북과 트위터가 있다.
* Post는 SNS 객체를 받아서 포스트를 뿌린다.
* Post의 구현체로는 현재 Text와 Picture가 있다.

```java
interface SNS {}

interface Post {
    void postOn(SNS sns);
}
class Twitter implements SNS {}

class Facebook implements SNS {}

class Text implements Post {
    public void postOn(SNS sns) {
        System.out.println("TEXT by " + sns.getClass().getSimpleName());
    }
}

class Picture implements Post {
    public void postOn(SNS sns) {
        System.out.println("PICTURE by " + sns.getClass().getSimpleName());
    }
}
```

<br>

이렇게 구현하고 비즈니스 로직에서 각 플랫폼 별로 포스팅을 한다.

```java
public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter());

    posts.forEach(post -> sns.forEach(s -> post.postOn(s)));
}
```

출력 결과

```
TEXT by Facebook
TEXT by Twitter
PICTURE by Facebook
PICTURE by Twitter
```

<br>

## 요구 사항 변경과 분기 처리를 이용한 설계의 문제점

여기서는 단순히 getClass로 각 SNS의 클래스를 찍어주는 동일한 로직이 사용되었다. 그런데 요구 사항이 변경되어서, 각 SNS 마다 로직을 달리 짜주어야 한다면 코드를 어떻게 변경해야 할까?

```java
class Text implements Post {

    public void postOn(SNS sns) {
        if (sns instanceof Facebook) {
            System.out.println("FACEBOOK UPLOADING");
            System.out.println("TEXT by facebook");
        }

        if (sns instanceof Twitter) {
            System.out.println("TWITTER UPLOADING");
            System.out.println("TEXT by twitter");
        }
    }
}

class Picture implements Post {

    public void postOn(SNS sns) {
        if (sns instanceof Facebook) {
            System.out.println("FACEBOOK UPLOADING");
            System.out.println("PICTURE by facebook");
        }

        if (sns instanceof Twitter) {
            System.out.println("TWITTER UPLOADING");
            System.out.println("PICTURE by twitter");
        }
    }
}
```

postOn() 메서드에서 `instanceof` 연산자로 타입을 분류해서 각 타입에 맞는 로직을 실행하도록 코드를 작성했다.

메인 메서드를 돌려보면 예상대로 잘 돌아간다. 이제 여기서 SNS로 인스타그램을 추가하라는 요구가 들어왔다.

```java
class Instagram implements SNS {}
```

Post 부분에도 인스타그램에 대한 로직을 추가한다.

```java
class Text implements Post {

    public void postOn(SNS sns) {
        if (sns instanceof Facebook) {
            System.out.println("FACEBOOK UPLOADING");
            System.out.println("TEXT by facebook");
        }

        if (sns instanceof Twitter) {
            System.out.println("TWITTER UPLOADING");
            System.out.println("TEXT by twitter");
        }

        if (sns instanceof Instagram) {
            System.out.println("INSTAGRAM UPLOADING");
            System.out.println("TEXT by instagram");
        }
    }
}

class Picture implements Post {

    public void postOn(SNS sns) {
        if (sns instanceof Facebook) {
            System.out.println("FACEBOOK UPLOADING");
            System.out.println("PICTURE by facebook");
        }

        if (sns instanceof Twitter) {
            System.out.println("TWITTER UPLOADING");
            System.out.println("PICTURE by twitter");
        }
    }
}
```

```java
public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter(), new Instagram());

    posts.forEach(post -> sns.forEach(s -> post.postOn(s)));
}
```

그런데 예상대로 돌아가지 않는다. 알고보니 Picture에 인스타그램에 대한 로직을 추가하는 것을 깜빡한 것이다. 지금은 코드가 짧아서 한눈에 보이지만, 어마어마한 양의 코드가 작성되어있다고 가정한다면, 버그를 찾기가 굉장히 어려울 것이다.

## 오버로딩을 이용한 설계와 문제점

하나의 메서드에 여러 분기문을 만들어서 코드를 관리하려니 추가, 수정도 힘들고 버그를 찾기도 어렵다. 그래서 오버로딩을 이용해 if 문을 사용하지 않고 로직을 분리하기로 했다.

```java
interface Post {

    void postOn(Facebook facebook);
    void postOn(Twitter twitter);
}
class Text implements Post {

    public void postOn(Facebook facebook) {
        System.out.println("FACEBOOK UPLOADING");
        System.out.println("TEXT by facebook");
    }

    public void postOn(Twitter twitter) {
        System.out.println("TWITTER UPLOADING");
        System.out.println("TEXT by twitter");
    }

}

class Picture implements Post {

    public void postOn(Facebook facebook) {
        System.out.println("FACEBOOK UPLOADING");
        System.out.println("PICTURE by facebook");
    }

    public void postOn(Twitter twitter) {
        System.out.println("TWITTER UPLOADING");
        System.out.println("PICTURE by twitter");
    }

}
```

예제를 간단하게 하기 위해 인스타그램은 빼고 로직을 수정했다. 이제 if 문으로 분기하지 않아도 되어 코드가 좀 더 간결해졌다.

하지만 이 경우에도, 새로운 SNS가 추가될 경우 Post와 Text, Picture를 모두 수정해야하는 것은 여전하다. 하지만 가장 심각한 문제는 main 메서드가 더이상 돌아가지 않는다는 것이다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbgj1qP%2FbtqTjDxgRyX%2FRJFV2ZRkslGiY8fDEkVY51%2Fimg.jpg)

postOn 메서드에 인자를 받는 부분에서 빨간줄이 그여져있는 것을 확인할 수 있다. 에러 메시지를 보면 "SNS 객체를 받는 postOn 메서드를 찾을 수 없다"며 컴파일 타임에서 에러를 내고 있다.

분명 Post의 postOn 메서드에 Facebook 객체와 Twitter 객체를 매개변수로 받는 메서드를 정의해주고 객체도 잘 넘겨줬는데, 메서드를 찾을 수 없다고 말하는걸까?

그 이유는, 메서드 오버로딩은 정적 메서드 디스패치를 하기 때문이다. 오버로딩된 메서드는 컴파일 시점에서 정확히 타입 체크를 하고 어떤 메서드를 실행할지 알아야 하는데, 
메인 메서드의 forEach문을 보면 매개변수로 Facebook이나 Twitter같은 특정 타입이 아니라 SNS 객체를 넘겨주고 있기 때문에, 어떤 메서드를 실행할지 결정할 수 없는 것이다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbkozzw%2FbtqS6XcN81p%2FYyHabiYqrNbaNtACBmi2gk%2Fimg.png)

<br>


## 더블 디스패치 기법 적용

이 문제를 해결하기 위해서 드디어 더블 디스패치가 등장할 시점이다. 조금 헷갈리니 차근차근 살펴보자.

우선 현재 문제는 오버라이딩이 아닌 오버로딩으로 구현하다 보니 정적 메서드 디스패치를 해야하는 제약 때문에 SNS 객체를 넘길 수가 없는 것이다. SNS 객체를 넘겨 받아서 메서드를 실행하려면 동적 메서드 디스패치를 사용해야하고, 그러려면 메서드 오버라이딩을 이용해야한다.

우선 postOn 메서드는 SNS 객체를 받는 것으로 다시 수정해준다.

```java
interface Post {
    void postOn(SNS sns);
}
```

<br>


그리고 Post의 구현체인 Facebook과 Twitter에 postOn을 오버라이딩 해준다.

```java
class Text implements Post {

    public void postOn(SNS sns) {

    }
}

class Picture implements Post {

    public void postOn(SNS sns) {
    
    }
}
```

<br>


이제 여기서 넘겨받은 SNS 객체로 뭔가를 해줘야하는데, 이를 위해서 SNS 인터페이스에 post 메서드를 만들어준다.

```java
interface SNS {
    void post(Text text);
    void post(Picture picture);
}
```

<br>


그리고 그 구현체인 Facebook과 Twitter 객체에 각각 text와 picture가 넘어왔을 때 해주던 작업을 해준다.

```java
class Facebook implements SNS {

    public void post(Text text) {
        System.out.println("FACEBOOK UPLOADING");
        System.out.println("TEXT by facebook");
    }

    public void post(Picture picture) {
        System.out.println("FACEBOOK UPLOADING");
        System.out.println("PICTURE by facebook");
    }
}

class Twitter implements SNS {
    public void post(Text text) {
        System.out.println("TWITTER UPLOADING");
        System.out.println("TEXT by twitter");
    }

    public void post(Picture picture) {
        System.out.println("TWITTER UPLOADING");
        System.out.println("PICTURE by twitter");
    }
}
```

<br>


이제 Text와 Picture에서는 매개변수로 전달받은 SNS 객체에 post를 호출하고 자기 자신을 매개변수로 넘겨준다.

```java
class Text implements Post {

    public void postOn(SNS sns) {
        sns.post(this);
    }
}

class Picture implements Post {

    public void postOn(SNS sns) {
        sns.post(this);
    }
}
```

<br>

전체 코드는 다음과 같다.

```java
public class Dispatch {

    public static void main(String[] args) {
        List<Post> posts = Arrays.asList(new Text(), new Picture());
        List<SNS> sns = Arrays.asList(new Facebook(), new Twitter());

        posts.forEach(post -> sns.forEach(s -> post.postOn(s)));
    }
}

interface Post {

    void postOn(SNS sns);
}

class Text implements Post {

    public void postOn(SNS sns) {
        sns.post(this);
    }
}

class Picture implements Post {

    public void postOn(SNS sns) {
        sns.post(this);
    }
}

interface SNS {
    void post(Text text);
    void post(Picture picture);
}

class Facebook implements SNS {

    public void post(Text text) {
        System.out.println("FACEBOOK UPLOADING");
        System.out.println("TEXT by facebook");
    }

    public void post(Picture picture) {
        System.out.println("FACEBOOK UPLOADING");
        System.out.println("PICTURE by facebook");
    }
}

class Twitter implements SNS {
    public void post(Text text) {
        System.out.println("TWITTER UPLOADING");
        System.out.println("TEXT by twitter");
}

    public void post(Picture picture) {
        System.out.println("TWITTER UPLOADING");
        System.out.println("PICTURE by twitter");
    }
}
```

<br>

이제 메인 메서드를 동작해보면 우리가 원하는대로 잘 돌아간다.

```
FACEBOOK UPLOADING
TEXT by facebook
TWITTER UPLOADING
TEXT by twitter
FACEBOOK UPLOADING
TEXT by facebook
TWITTER UPLOADING
TEXT by twitter
```

<br>

이렇게 동적 디스패치가 두 번 적용되기 때문에 이 기법을 더블 디스패치라고 한다.

```java
interface Post {

    void postOn(SNS sns);   // 여기서 한 번
}
class Text implements Post {

    public void postOn(SNS sns) {
        sns.post(this);    // 여기서 한 번
    }
}
```

<br>

그럼 이렇게 하면 뭐가 좋냐? 아까처럼 인스타그램을 다시 추가해보자.

```java
class Instagram implements SNS {
    public void post(Text text) {
        System.out.println("INSTAGRAM UPLOADING");
        System.out.println("TEXT by instagram");
    }

    public void post(Picture picture) {
        System.out.println("INSTAGRAM UPLOADING");
        System.out.println("PICTURE by instagram");
    }
}
```

이렇게 텍스트와 사진을 업로드하는 로직을 가진 Instagram 클래스를 설계해준다.

<br>

```java
public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter(), new Instagram());

    posts.forEach(post -> sns.forEach(s -> post.postOn(s)));
}
```

그리고 SNS 리스트에 인스타그램을 추가해준다.

메인 메서드를 실행해보면 잘 돌아간다.

```
FACEBOOK UPLOADING
TEXT by facebook
TWITTER UPLOADING
TEXT by twitter
INSTAGRAM UPLOADING
TEXT by instagram
FACEBOOK UPLOADING
PICTURE by facebook
TWITTER UPLOADING
PICTURE by twitter
INSTAGRAM UPLOADING
PICTURE by instagram
```

더블 디스패치를 적용하지 않았을 때와 비교해보면, 실질적으로 기존 코드를 수정한 부분이 없다.
아까는 분기문을 추가하기 위해서 Post 구현체들의 코드를 수정해야 했는데, 지금은 그저 새로운 SNS 구현체를 정의해주기만 하면된다. 

즉 수정에는 닫혀있고 확장에는 열려있는 OCP를 지키고 있는 것이다.