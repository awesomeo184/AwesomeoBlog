---
title: '[OS] 세마포어(Semaphore)'
date: 2021-04-14 13:04:63
category: OS
thumbnail: { thumbnailSrc }
draft: false
---

동기화 방법에는 여러 가지가 있으며 하드웨어적으로 해결하는 방법과 소프트웨어적으로 해결하는 방법으로 나뉩니다.
여기서는 소프트웨어적 해결 방안 중 하나인 세마포어에 대해 알아보겠습니다.

세마포어는 동기화 문제 해결을 위해 네덜란드의 컴퓨터 과학자 에츠허르 다익스트라(Edsger Wybe Dijkstra)가 제안한 **소프트웨어 도구**입니다. 추상적인 설명보다는 세마포어를 직접 사용하면서 차근차근 알아가봅시다.

세마포어의 구조는 **한 개의 정수형 변수**와 **두 개의 동작**으로 구성되어 있습니다. 두 개의 동작은 다음과 같이 불립니다.

* P = Proberen(test) = wait = acquire
* V = Verhogen(increment) = signal = release

Proberen&Verhogen은 네덜란드어이고, 보통 OS 교과서에서는 wait&signal로 부릅니다. acquire&release는 java.util.concurrent.Semaphore에 정의된 wait과 signal의 메서드명입니다.

```java
class Semaphore {
    int value;   // 한 개의 정수형 변수. number of permit
    
    Semaphore(int value) {
        ...
    }
    
    // 두 개의 동작 
    void acquire() {
        ...
    }
    
    void release() {
        ...
    }
}
```

<br>

각각의 동작은 쓰레드의 실행을 제어합니다.

```java
void acquire() {
    value--;
    if (value < 0) {
        호출한 프로세스 혹은 쓰레드를 잡아서 대기 큐에 가둔다.
    }
}
void release() {
    value++;
    if (value <= 0) {
        대기 큐에 있는 쓰레드를 깨운다.
    }
}
```

각각의 세마포어는 연관된 **대기 큐(Waiting Queue)**를 하나씩 갖고 있는데, value 값에 따라 쓰레드를 대기 큐에 넣기도 하고, 대기 큐에 들어있는 쓰레드를 내보내기도 합니다. 
그리고 acquire와 release 연산은 **반드시 원자적으로(atomically) 실행**됨이 보장되어야 합니다. 즉 블록 안에서 문맥 전환이 일어나지 않습니다.

만약 semaphore의 value의 값이 0인 상태에서 임의의 쓰레드 a가 semaphore.acquire()를 호출하면, value의 값이 1 줄어들면서 if문의 조건을 만족하게 됩니다. 
if문의 블럭이 실행되면 a는 해당 세마포어가 관리하는 대기 큐로 들어가게 되며 대기 상태가 됩니다. 그러다 다른 쓰레드 b가 semaphore.release()를 호출하면 value의 값이 1 증가하고 if문의 블럭이 실행되면서 대기 큐에서 기다리고 있던 a가 깨어나게 됩니다.

그림으로 나타내면 다음과 같습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FccrGqX%2Fbtq2nodgDaT%2FuxtyBdi0AKQFm2VKfKwgc0%2Fimg.png)

그럼 세마포어로 어떻게 임계 구역 문제를 해결할 수 있을까요?

우선 상호 배타(Mutual exclusion)를 위한 세마포어의 일반적인 사용법은 다음과 같습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FptifB%2Fbtq2iEHW4RL%2FZrvTtO5WANM5sBK9rDul4k%2Fimg.png)
출처: 양희재 교수님 운영체제 수업 강의자료

초기 value를 1로 설정하고 임계 구역의 앞뒤로 acquire와 release를 호출합니다. 이렇게 하면 임계 구역에는 단 하나의 쓰레드만 들어갈 수 있습니다. 왜 그럴까요? 
천천히 생각해봅시다. 같은 임계 구역을 실행하는 두 개의 쓰레드 t1과 t2가 돌아간다고 가정해봅시다.

```java
void acquire() {
    value--;
    if (value < 0) {
        호출한 프로세스 혹은 쓰레드를 잡아서 대기 큐에 가둔다.
    }
}
void release() {
    value++;
    if (value <= 0) {
        대기 큐에 있는 쓰레드를 깨운다.
    }
}
```
위 두 코드를 보면서 천천히 실행 흐름을 따라가 보세요.

> 초기 value는 1입니다. 첫 번째 쓰레드 t1이 실행됩니다.
> 
> 임계 구역으로 들어가기 전에 acquire를 호출합니다.
> 
> value가 1 줄어 0이 됩니다. 0보다 작지 않기 때문에 acquire의 if문 블록을 건너뛰고 임계 구역에 진입합니다.
> 
> 임계 구역의 코드를 실행하는 도중 문맥 전환이 일어납니다.
> 
> 두 번째 쓰레드 t2가 실행됩니다.
> 
> 임계 구역으로 들어가기 전에 acquire를 호출합니다.
> 
> value가 -1이 됩니다. 0보다 작기 때문에 acquire의 if문 블록을 실행합니다.
> 
> t2는 임계 구역의 코드를 실행하지 못한 채로 waiting queue로 들어가면서 대기 상태가 됩니다.
> 
> t1이 다시 실행됩니다. 멈췄던 부분부터 다시 실행합니다.
> 
> 임계 구역의 코드를 모두 실행하고 release를 호출합니다.
> 
> value가 0이 됩니다. 0보다 같거나 작기 때문에 release의 if문 블록을 실행합니다.
> 
> waiting queue에서 기다리던 t2가 깨어나 임계 구역을 실행합니다.
> 
> t2에서 release를 호출합니다. value가 1이 됩니다.
> 
> 처음 상태로 돌아갑니다.

이해가 되시나요?

예시로 확인해봅시다.

[이전 포스팅](https://awesomeo184.netlify.app/OS/critical_section_and_race_condition/)에서 살펴본 BankAccount의 deposit 메서드를 다시 살펴봅시다. 이 메서드는 실행블록 전체가 임계 구역입니다. withdraw도 마찬가지입니다.

```java
public void deposit(int amount) {
    /////////////////////////////// 전체가 임계 구역
    int tmp = balance + amount;
    System.out.print("+");
    balance = tmp;
    ///////////////////////////////
}
```

<br>

BankAccount 내에 Semaphore 객체를 생성하고 정수값을 1로 초기화합니다.
```java
class BankAccount {

    int balance = 0;
    Semaphore semaphore = new Semaphore(1);    //int value = 1;

    public void deposit(int amount) {
        int tmp = balance + amount;
        System.out.print("+");
        balance = tmp;
    }
    
    ...
}
```

<br>

그리고 각 임계 구역의 앞뒤에서 acquire와 release를 호출합니다.

```java
public void deposit(int amount) {
    try {
        semaphore.acquire();
    } catch (InterruptedException e) {}
    
    /////////////////////////////////////
    int tmp = balance + amount;
    System.out.print("+");
    balance = tmp;
    /////////////////////////////////////
    
    semaphore.release();
}
```

acquire 메서드는 InterruptedException을 던지기 때문에 예외처리를 해줬습니다. withdraw 메서드도 똑같이 처리해줍니다.

```java
public void withdraw(int amount) {
    try {
        semaphore.acquire();
    } catch (InterruptedException e) {}
    
    /////////////////////////////////////
    int tmp = balance - amount;
    System.out.print("-");
    balance = tmp;
    /////////////////////////////////////
    
    semaphore.release();
}
```

<br>

전체 코드는 다음과 같습니다. 실행하면 어떤 결과가 나올까요? 코드량이 좀 많지만 천천히 읽어보면서 결과를 예상해봅시다.

```java
public class SynchronizationDemo {

    public static void main(String[] args) throws InterruptedException {
        BankAccount account = new BankAccount();

        Parent parent = new Parent(account);
        Child child = new Child(account);

        parent.start();
        child.start();

        // 두 쓰레드가 끝날 때까지 대기
        parent.join();
        child.join();

        //잔액 출력
        System.out.println("잔액 = " + account.getBalance());
    }

}

class BankAccount {

    int balance = 0;
    Semaphore semaphore = new Semaphore(1);

    public void deposit(int amount) {
        try {
            semaphore.acquire();
        } catch (InterruptedException e) {}

        /////////////////////////////////////
        int tmp = balance + amount;
        System.out.print("+");
        balance = tmp;
        /////////////////////////////////////

        semaphore.release();
    }

    public void withdraw(int amount) {
        try {
            semaphore.acquire();
        } catch (InterruptedException e) {}

        /////////////////////////////////////
        int tmp = balance - amount;
        System.out.print("-");
        balance = tmp;
        /////////////////////////////////////

        semaphore.release();
    }

    public int getBalance() {
        return balance;
    }
}

class Parent extends Thread {

    BankAccount account;

    public Parent(BankAccount account) {
        this.account = account;
    }

    @Override
    public void run() {
        for (int i = 0; i < 1000; i++) {
                account.deposit(1000);
        }
    }
}

class Child extends Thread {
    BankAccount account;

    public Child(BankAccount account) {
        this.account = account;
    }

    @Override
    public void run() {
        for (int i = 0; i < 1000; i++) {
            account.withdraw(1000);
        }
    }
}
```

<br>

실행 결과

```
+---------------+++++++---++--------++++--++----+-++++++++++-+++++++-++++++--
-+++---------++++++++++-------++++++++++++++----+++++++--------++++----------
... 잔액 = 0
```


몇 번을 실행하든 잔액은 0으로 출력됩니다. 이렇게 세마포어를 이용해 임계 구역 문제를 해결해보았습니다.



## 참고자료

Operating System Concepts

양희재 교수님 KOCW 운영체제 강의 (www.kocw.net/home/search/kemView.do?kemId=978503)