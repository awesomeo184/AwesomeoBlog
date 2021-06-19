---
title: '[OS] 임계구역과 경쟁상태 그리고 동기화'
date: 2021-04-13 13:04:63
category: OS
thumbnail: { thumbnailSrc }
draft: false
---
목표: 멀티 프로세스/쓰레드 환경에서 핵심적인 문제인 동기화가 무엇인지 살펴봅니다. 
BankAccount 예제를 통해서 경쟁 상태와 임계 구역 문제에 대해 알아봅니다.

쓰레드에 대한 기본 개념이 없으신 분들은 [이 글](https://wisdom-and-record.tistory.com/48)을 참조해주세요.

# 경쟁상태와 임계구역

현대의 운영체제 대부분은 멀티 쓰레드를 지원합니다. 근데 이때 공유 자원(shared data)에 여러 쓰레드가 접근해 값을 수정하면 데이터의 일관성(consistency)이 깨지게 되는데, 이를 경쟁 상태(Race Condition)라고 합니다. 이 경쟁 상태를 해결하기 위한 것이 동기화(Synchronization)입니다. 예를 들어보죠.

하나의 은행 계좌(공유 자원)에 부모님은 돈을 입금하고 자식은 돈을 출금하는 상황을 코드로 구현해 보겠습니다.

우선 예금 잔액(balance)과 입금, 인출 메서드를 가진 BankAccount 클래스를 작성합니다.

```java
class BankAccount {
    int balance = 0;

    public void deposit(int amount) {
        balance += amount;
    }

    public void withdraw(int amount) {
        balance -= amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

<br>

그리고 한 쪽은 1000원씩 계속 입금하고, 한 쪽은 1000원씩 계속 인출하는 부모 쓰레드와 자식 쓰레드를 작성합니다.

```java
class Parent extends Thread {

    BankAccount account;

    public Parent(BankAccount account) {
        this.account = account;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
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
        for (int i = 0; i < 100; i++) {
            account.withdraw(1000);
        }
    }
}
```

<br>

자 이제 이 두 쓰레드를 돌려봅시다. 두 쓰레드가 서로 번갈아가면서 실행되긴 하지만, 결론적으로는 각각 돈을 1000원씩 100번 입금하고 인출하기 때문에 두 쓰레드가 끝난 후에 계좌에 남은 돈은 반드시 0이 되어야 합니다.

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
```

<br>

실행 결과

```
잔액 = 0
```

<br>

예상대로 0원이 출력됩니다. 겉보기에는 Race condition 상태가 아닌 것처럼 보이지만, 이는 연산이 너무 빨리 일어나서 문제가 생길 틈이 없었기 때문입니다. 문제의 조건은 바꾸지 않은 채로 Race condition이 발생하도록 연산량을 조금 늘려보도록 하겠습니다.

```java
public void deposit(int amount) {
    // balance += amount;
    int tmp = balance + amount;
    System.out.print("+");
    balance = tmp;
}

public void withdraw(int amount) {
    //balance -= amount;
    int tmp = balance - amount;
    System.out.print("-");
    balance = tmp;
}
```
기존의 문장을 tmp를 추가해서 두 줄로 늘리고 사이에 인출, 입금이 일어날 때마다 +와 -를 출력하도록 해주었습니다.

<br>

```java
@Override
public void run() {
    for (int i = 0; i < 1000; i++) {
        account.withdraw(1000);
    }
}
```
또 인출과 입금도 100회에서 1000회로 늘려줍니다. 코드량이 늘긴 했지만 여전히 최종 금액은 0이 나와야 합니다.

<br>

하지만 코드를 실행해보면,
```
++--+++++++++++++++++++++++++-------------------------++++++++++-----++
-------+++++++-----------------++++++++--------------------------+-----
...
...잔액 = 1000000
```

갑자기 잔액이 이상하게 바뀝니다. 코드를 여러 번 실행해보면, 양수도 됐다가 음수도 됐다가하면서 값이 제멋대로 바뀝니다.

실행하는 코드가 논리적으로 바뀐 부분은 전혀 없기 때문에 잔액은 0원으로 나오는 것이 정상입니다. 왜 이런 일이 발생할까요? 앞서 말했듯이 경쟁 상태로 인해 데이터의 일관성이 깨진 것입니다. 이 상황을 풀어서 설명하면 다음과 같습니다.

우선 제일 처음 Parent 쓰레드가 실행됩니다. run 메서드가 실행되어 첫 번째 for-loop를 돕니다. 이때 bankAccount 객체의 잔액은 0원 입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbcGBEW%2Fbtq2isgAvdK%2FTn1mH5JGf6q9VmlupoYXLk%2Fimg.png)

<br>
<hr>
<br>

그러다 두 번째 코드를 실행하는 도중 문맥 전환(Context switching)이 일어나고, Child 쓰레드가 실행됩니다. Parent 쓰레드는 문맥이 전환되기 전에 연산의 내용을 저장합니다.

문맥 전환이 일어난 후, Child 쓰레드는 모든 코드를 수행합니다. 이전에 Parent 쓰레드는 3번 코드를 실행하지 못하고 중단이 되었기 때문에 balance는 여전히 0인 상태입니다. 그리고 Child 쓰레드가 실행되고 3번 코드를 수행하면서 공유 자원인 balance는 -1000이 됩니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FqIsrR%2Fbtq2gQbEP7q%2FEgHUBNLXCgIlidPz3eh5fk%2Fimg.png)

<br>
<hr>
<br>

child 쓰레드의 for-loop가 한번 실행된 후 다시 문맥 전환이 되어 Parent 쓰레드가 실행됩니다. Parent 쓰레드는 문맥 전환이 되기 전에 저장해뒀던 tmp를 다시 불러와서 연산을 수행합니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FQ7tKV%2Fbtq2gKCLglj%2FHfmA07qWK3ntkDKFDVL4o1%2Fimg.png)

balance 값은 1000으로 덮어씌워집니다. 원래대로라면 deposit이 한번, withdraw가 한번 수행되었기 때문에 balance는 0이 되어야 하지만, deposit 코드가 실행되는 과정에서 문맥 전환이 일어나 기대했던 값과 달라지게 되었습니다. 이를 **데이터의 일관성(Consistency)이 깨졌다**고 표현합니다.

제가 비록 설명을 위해 코드를 세 줄로 늘렸지만 코드가 한 줄이라도 이런 상황은 일어납니다. BankAccount 클래스를 처음 작성했을 때의 코드로 돌아가봅시다.

```java
class BankAccount {
    int balance = 0;

    public void deposit(int amount) {
        balance += amount; //이 부분!
    }
    ...
}
```

위 코드는 자바 코드로는 한 줄이지만 어셈블리로 변환하면 다음과 같이 네 줄이 됩니다.

```
LDR r0, [balance]
LDR r1, [amount]
ADD r0, r0, r1
STR r0, [balance]
```

위 네 개의 명령은 반드시 **원자적(atomic)**이어야 합니다. 즉 분해되지 않고 한번에 처리되어야 합니다. 
만약 3번째의 ADD 명령을 수행하다가 문맥 전환이 일어나서 balance의 값이 원래 값과 달라지면 위에서 설명한 상황처럼 데이터의 일관성이 깨지게 되는 것입니다.

이처럼 둘 이상의 쓰레드가 동시에 접근해서는 안되는 공유 자원(여기선 balance)을 접근하는 코드의 일부를 **임계 구역(Critical Section)**이라고 합니다.

# 동기화

동기화는 이렇게 여러 쓰레드가 동시에 실행되는 과정에서도 데이터의 일관성을 유지할 수 있도록 임계 구역 문제를 해결하고 프로세스/쓰레드의 실행 순서를 제어하는 작업을 말합니다. 앞서 말한 임계 구역이 올바르게 실행되기 위해서는 세 가지 조건이 반드시 충족되어야 합니다.

* Mutual exclusive(상호배타): 임계 구역에는 오직 하나의 쓰레드만 진입해야 한다.
* Progress(진행): 임계 구역을 실행 중인 쓰레드가 없다면, 다른 쓰레드가 임계 구역에 진입하는 것은 유한 시간 내에 결정되어야 한다.  
* Bounded Waiting(한계 대기): 어떤 프로세스가 임계 구역에 진입하기를 요청한 후에는 유한 시간 내에 진입이 허용되어야 한다.

<br>

말이 어려워서 이해가 잘 안될 수 있는데요, 임계 구역을 화장실 변기 칸이라 치고 쓰레드를 사람이라 치면 다음과 같습니다.

일단 1번 상호배타는 화장실에는 반드시 한 사람만 들어가야 한다는 것입니다. 위 예시에서는 Parent가 balance를 수정하고 있는데 도중에 Child가 들어와서 balance의 값을 수정하면 안됩니다.

상호 배타가 지켜졌다고 해서 임계 구역 문제가 해결되는 것은 아닙니다. 나머지 두 조건도 반드시 만족해야 하는데요,

2번 진행은 쉽게 말하면 임계 구역에 들어가 있는 쓰레드가 없으면, 다른 쓰레드가 임계 구역에 접근하는 것이 보장되어야 한다는 것입니다. 예를 들어 화장실이 비어있어서 내가 들어가려고 하는데, 화장실 밖에 있는 다른 사람들이 나를 못들어가게 막으면 안된다는 것입니다.

3번 한계 대기는 어떤 프로세스가 임계 구역에 대한 진입을 요청했는데, 이를 무한정 기다리게 해서는 안된다는 것입니다. 내가 화장실 앞에 줄서서 기다리고 있는데, 안에 있는 사람이 문을 걸어 잠그고 무한히 나오지 않거나, 나오면서 문을 잠궈버리고 열쇠를 들고 도망가면 안된다는 것이지요.

다음 포스팅에서 전통적인 동기화 도구인 세마포어에 대해 알아보겠습니다.

### 참고 자료

Operating System Concepts

양희재 교수님 KOCW 운영체제 강의 (www.kocw.net/home/search/kemView.do?kemId=978503)
