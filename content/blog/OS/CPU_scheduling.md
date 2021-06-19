---
title: '[OS] CPU 스케줄링과 스케줄링 알고리즘'
date: 2021-04-12 13:04:63
category: OS
thumbnail: { thumbnailSrc }
draft: false
---

* 목표: CPU 스케줄링과 관련 알고리즘에 대해 알아봅니다.

# CPU scheduling

CPU 스케줄링(Scheduling)이란 **어떤 프로세스가 CPU를 점유할지를 결정하는 것**을 말한다. 
CPU 스케줄링의 목적은 시스템을 효율적이고 빠르고 공정(fair)하게 만드는 것이다. CPU 스케줄링은 다음과 같은 기준으로 평가될 수 있다.

* CPU 이용률(CPU Utilization): 단위는 %
* 처리율(Throughput): 시간 당 몇 개의 작업을 처리하는가? (Jobs/sec)
* 반환시간(Turnaround Time): 하나의 프로세스가 작업을 시작하고부터 종료할 때까지 걸린 시간 (sec)
* 대기시간(Waiting Time): 레디 큐에서 기다린 시간 (sec)
* 응답시간(Response Time): (보통 대화형 시스템에서) 사용자가 요청을 한 시점부터 응답이 나올 때까지 걸리는 시간 (sec)

## 선점(Preemptive) vs 비선점(Non-preemptive)

CPU 스케줄링에는 선점 방식과 비선점 방식이 있다. 선점 방식은 프로세스가 아직 CPU를 더 점유할 수 있음에도 다른 프로세스에게 CPU 점유를 빼앗길 수 있는 방식의 스케줄링이다. 
이때 아직 CPU를 더 점유할 수 있다는 말은 다음과 같은 상황이 아닌 경우들을 말한다.

* running -> terminated
* running -> waiting

위 상황에서는 프로세스가 무슨 용 빼는 재주가 있지 않은 이상 CPU를 놓아주어야 한다.
프로세스가 모든 실행을 마치고 종료 상태가 될 때, 이 프로세스가 CPU를 더 점유할 수 있을까? 없다. 이미 모든 실행을 마친 상태이므로 다른 프로세스에게 CPU를 넘기고 종료되어야 한다.
또 실행 상태에서 I/O를 만나거나 다른 Waiting event를 만났을 때, CPU에 더 머무를 방법이 있을까? 없다. 마찬가지로 다른 프로세스에게 CPU를 넘기고 waiting queue로 이동해야한다.

이같은 상황이 아닌데도 다른 프로세스에게 CPU 점유를 넘겨준다면, 예를 들어 running 상태에서 바로 ready 상태가 되는 경우, 이는 선점 스케줄링이다. 비선점 방식은 이와 반대이다. 위에서 언급한 두 가지 상황에서만 context switching이 일어난다면 이는 비선점 스케줄링이다.

병원에 비유하면, 환자가 진료실에 들어가서 진료를 마칠 때까지 나오지 않는 것이 비선점, 중간에 응급 환자가 도착해서 진료실에서 쫓겨나는 것이 선점이다.

# CPU 스케줄링 알고리즘

## FCFS (First-Come, First-Served)

이름 그대로 먼저 도착한 프로세스를 먼저 처리하는 방식이다. 아래의 간트차트를 보자.

|Process|Burst Time (msec)|
|:---:|:---:|
|P1|24|
|P2|3|
|P3|3|

Burst Time: 프로세스가 CPU에서 머무른 총 시간

프로세스들이 P1, P2, P3 순서로 거의 동시에 레디 큐에 도착했다고 하자. FCFS 알고리즘으로 스케줄링을 했을 때, 프로세스들의 평균 대기 시간(Average Waiting Time)은 다음과 같이 구한다.

P1은 도착하자마자 CPU를 점유하므로 대기시간은 0 msec. P2는 P1의 버스트타임만큼 기다리므로 24 msec, P3는 앞의 두 프로세스를 모두 기다리므로 24 + 3 = 27 msec. 따라서 AWT = ( 0 + 24 + 27 ) / 3 = 17 msec

FCFS는 가장 간단하고 공정한 방식이지만 대기 시간이 상대적으로 많다. 특히 위 예처럼 가장 먼저 도착한 프로세스가 반환시간이 아주 긴 경우, 뒤에 도착한 프로세스들이 오랫동안 기다려야하는 문제가 발생한다. 이를 호위 효과(Convoy effect)라고 한다. 마치 P1을 P2, P3가 호위하고 있는 것처럼 보인다는 뜻에서 붙은 이름이다.

**FCFS는 대표적인 Nonpreemptive 스케줄링 방식이다.**

## SJF (Shortest Job First) / Non-preemptive

SJF 방식은 가장 적은 버스트 타임을 가지는 프로세스를 먼저 실행하는 스케줄링 방식이다. 예제로 살펴보자.

|Process|Burst Time (msec)|
|---|---|
|P1|6|
|P2|8|
|P3|7|
|P4|3|

버스트 타임이 짧은 순서대로 처리하면 P4> P1 > P3 > P2 순서로 프로세스가 처리된다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FB8UMn%2Fbtq2lBd4yFl%2FOyOHGnxfFQRintxDfdUsC1%2Fimg.png)

AWT = (0 + 3 + 9 + 16) / 4 = 7 msec


만약 같은 상황에서 FCFS 알고리즘으로 처리한다면 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F8L4ia%2Fbtq2irccADw%2FPvtY4mBWkKdJK1myKbJIj0%2Fimg.png)

AWT = ( 0 + 6 + 14 + 21 ) / 4 = 10.25 msec로 3msec 정도 차이가 난다.

결과만 놓고 보면 SJF 알고리즘이 가장 최적의 알고리즘처럼 보이지만, 한 가지 고려해야 할 것이 있다. 
**이 방식을 사용하려면 각 Process의 버스트 타임을 미리 예측할 수 있어야 한다**는 것이다. 
여러 방법을 통해 프로세스의 버스트 타임을 예측할 수는 있지만 추가적인 오버헤드가 발생한다.

## SJF / Preemptive

최소 잔여 시간 우선(Shortest-Remaining-Time-First) 알고리즘이라고도 불리는 이 방식은 위에서 살펴본 것을 Preemptive한 방식으로 처리하는 것이다. 설명의 용이성을 위해 이번에는 각 프로세스의 도착시간에 차이가 있다고 생각해보자.

|Process|	Arrival Time|	Burst Time|
|---|---|---|
|P1|	0|	8|
|P2|	1|	4|
|P3|	2|	9|
|P4|	3|	5|

이번에는 프로세스의 버스트 타임을 확인하여 더 짧은 시간을 가지는 프로세스가 CPU를 선점하도록 만든다.

먼저 P1이 도착해서 CPU를 점유한다. 1 msec 후 P2가 도착한다.

이 때, P1은 1msec 동안 실행되었으므로 버스트 타임은 7 msec, P2의 버스트 타임은 4 msec이다.

P2가 더 짧은 버스트 타임을 가지므로 P2가 CPU를 선점한다. 같은 방식으로 처리를 진행한다. 결과적으로,

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcyQo6H%2Fbtq2oR1OLi7%2Fm1skN0iKKG4SeQRtScn2S0%2Fimg.png)

AWT는 6.5 msec이다. 같은 조건에서 Non-preemptive로 처리하면 7.75 msec의 시간이 걸린다. AWT만 놓고 보면 선점 스케줄링이 더 빠른 것을 확인할 수 있다. 물론 추가적인 context switching overhead가 발생한다.

## Priority Scheduling

각각의 프로세스에 우선 순위(보통 정수형 자료형을 사용)를 부여하고 우선 순위가 높은 순서대로 처리한다. 일반적으로 숫자가 낮을수록 우선 순위가 높다 (UNIX / Linux)

|Process|	Burst Time|	Priority|
|---|---|---|
|P1|	10|	3|
|P2|	1|1|
|P3|	2|4|
|P4|	1|5|
|P5|	5|2|

P2 > P5 > P1 > P3 > P4 순서로 처리한다. 계산해보면 AWT는 8.2 msec이다.

그럼 우선 순위는 어떤 기준으로 부여하면 좋을까? 상황에 따라 다르겠지만 내부적 요인과 외부적 요인으로 나누어볼 수 있다.

* 내부적 요인: time limit, memory requirement, I/O or CPU Burst
* 외부적 요인: 돈을 많이 내는 쪽(서버 컴퓨터의 경우), 정치적 이유 등

또 다른 조건으로는 Preemptive로 만들 것인가, Non-preemptive로 만들 것인가에 따라 결과가 달라질 수 있다.

어떤 방법이 더 좋은가는 주어진 상황에 따라 달라진다.

우선 순위 스케줄링에서 문제가 될 수 있는 것은 프로세스의 **기아 상태(Starvation)**을 야기할 수 있다는 것이다. 
기아 상태란, 우선 순위가 낮은 프로세스가 레디 큐에서 대기하고 있는데, 새로 들어오는 프로세스가 계속 해당 프로세스보다 우선 순위가 높아서, CPU를 전혀 점유하지 못하는 상황을 말한다. 이에 대한 해결책으로는 어떤 프로세스가 레디 큐에서 오래 머무르면 점진적으로 해당 프로세스의 우선 순위를 높여주는 aging 기법이 있다.

## Round Robin

라운드 로빈은 일정 시간이 지나면 다음 프로세스에게 CPU 점유를 넘기는 방식을 말한다. 옛날 만화 영화에서 뿅망치로 머리를 때리면 머리 위에서 참새가 빙글빙글 도는 모양을 떠올리면 된다. 시분할 시스템(time sharing system)에서 많이 쓰는 방식이다.

Time quantum( or time slice)를 정해놓고, 이 시간이 지나면 프로세스가 끝나지 않았더라도 CPU 점유를 다음 프로세스로 넘긴다. 대표적인 Preemptive 방식의 스케줄링이다.

예제를 보자. Time quantum이 4 msec이고, 도착 순서와 버스트 타임이 다음과 같다.

|Process|	Burst Time (msec)|
|---|---|
|P1|	24|
|P2|	3|
|P3|	3|

먼저 P1이 CPU를 점유한다. 4msec가 지나면 P2가 CPU를 선점한다. 3msec 후 P2는 종료되고 CPU는 P3로 넘어간다. 다시 3msec가 지나면 P1으로 CPU가 넘어오고, 프로세스가 P1 혼자 남았으므로 P1이 종료될 때까지 CPU를 점유한다.

![image](https://user-images.githubusercontent.com/63030569/122638111-3fdb4400-d12d-11eb-9f1b-a775be198414.png)

Round Robin 알고리즘의 퍼포먼스는 time quantum에 의존적이다. time quantum이 무한에 가까워질수록 퍼포먼스는 FCFS와 같아진다. time quantum이 0에 가까워질수록 context switching이 매우 빈번하게 일어나므로 오버헤드도 증가한다.
