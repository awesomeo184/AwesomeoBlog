---
title: '[OS] 프로세스란?'
date: 2021-04-11 13:04:63
category: OS
thumbnail: { thumbnailSrc }
draft: false
---

# 프로세스란?

## Program vs Process

프로그램 그 자체로는 아무 일도 할 수 없다. 프로그램은 그저 하드디스크에 저장되어 있는 하나의 파일, 즉 수동적인 존재일 뿐이다.
프로그램이 사용자에게 유용한 기능을 제공하기 위해서는 메모리에 로드되어서 프로그램 카운터(PC) 및 관련 자원의 집합을
가진 능동적인 존재로서 동작해야한다.

예를 들어 바탕화면에 있는 Word 프로그램은 그 자체로는 아무 의미가 없다. 우리가 마우스를 올려서
더블클릭을 하고 실행이 되어서, 흰 바탕에 커서가 깜빡거리는 순간 유용한 무언가가 되는 것이다.

그래서 프로세스는 비공식적으로 `실행 중인 프로그램(Program in execution)`이라고 불린다.

프로그램이 하나의 프로세스가 되는 과정은 다음과 같다. 사용자가 프로그램을 시작해달라는 요청(아이콘을 더블클릭 하는 등)을 보내면
OS는 하드디스크에서 해당 프로그램을 메인 메모리로 로드하면서, 필요한 자원을 할당한다. 그림으로 나타내면 다음과 같다.

![image](https://user-images.githubusercontent.com/63030569/115200281-fff8e000-a12e-11eb-8ec2-a397d1e310ed.png)


메모리 상에 존재하는 프로세스는 위 그림에서 나타난 것과 같은 구조를 가지는데, 각 섹션에 저장되어 있는 내용들은 다음과 같다.

* Text - 실행 가능한 코드 집합
* Data - 전역 변수들
* Heap - 런타임에 동적으로 메모리에 할당되는 데이터, 예를 들어 실행 중에 생성되는 인스턴스들에 대한 정보
* Stack - 함수가 실행될 때 일시적으로 생기는 데이터들, 예를 들어 지역 변수, 파라미터, 복귀 주소 등

<br />
Text와 Data 영역의 크기는 고정되어 있지만 Heap과 Stack 영역의 크기는 런타임에 수시로 변경된다.

## 프로세스의 상태

프로세스는 다섯 가지 상태 중 하나를 가진다.

* 생성(new): 갓 생성된 상태, OS의 커널에 PCB 등이 생성된다.
* 준비(ready): CPU의 서비스를 받기 위해 Ready Queue에서 대기하고 있는 상태
* 실행(running): CPU에 의해 실행되고 있는 상태
* 대기(waiting or blocked): I/O나 다른 event를 기다리며 멈춰있는 상태
* 종료(terminated): 실행을 완료하는 등의 이유로 종료된 상태

<br />

![프로세스 상태 전이도](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbGxrUj%2Fbtq2iVXMJOM%2F61klL3tYachPCWMigZiDvK%2Fimg.png)

## PCB(Process Control Block)란?

각 프로세스는 운영체제에서 `프로세서 제어 블록(PCB)`에 의해 표현된다(리눅스에서는 TCB, 태스크 제어 블록이라고 부른다).

각 프로세스는 하나의 PCB를 가지며 PCB에는 해당 프로세스에 대한 모든 정보, 
이를테면 프로세스의 상태(running, waiting, ready...), Program Counter,
registers, MMU info, CPU 사용시간, Process id 등을 저장하고 있다.

> TMI: 리눅스에서는 task_struct라는 이름의 구조체로 PCB를 구현하고 있다.

프로세스는 OS의 프로세스 관리 부서에서 관리하는데(OS를 정부에 빗대어서 생각해보자!), 이 곳에 PCB가 저장되어 있다.

![PCB](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeKgCWo%2Fbtq2npjl5dZ%2FpYbh3TtQ9AiUk6t6M9kVjk%2Fimg.png)


# Process Scheduling

## Scheduler & Queues

하드디스크에는 수많은 프로그램이 있다. 이 중 일부만이 메인 메모리에 올라올 수 있다. 또 메인 메모리에 올라온 것 중 일부만이 CPU 서비스를 받을 수 있다. 
서비스를 받아야 하는 것들은 많은데, 이를 다 수용할 수는 없기 때문에 줄을 서서 기다릴 수밖에 없는데, 이 줄도 OS가 관리한다.

우선 하드웨어에 있는 프로그램들을 Job이라고 부르는데, 이들이 메인 메모리에 올라가려면 먼저 `Job Queue`에서 대기해야 한다. 
Job Queue에서 대기 중인 프로그램 중 무엇이 먼저 메인 메모리에 올라갈까? 일반적으로 먼저 온 프로그램이 먼저 올라가겠지만, 그렇지 않은 경우도 있다.
이때, 무엇을 먼저 메인 메모리에 올릴 것인가를 OS의 `Job Scheduler`가 결정한다.

메인 메모리에 올라왔다고 해서 바로 실행될 수 있는 것은 아니다. CPU의 서비스를 받기 위해서는 다시 `Ready Queue`에서 
대기한다. Ready Queue에서 대기 중인 프로세스 중 무엇을 CPU 서비스를 받도록 할 것인가 결정하는 것이 `CPU Scheduler`이다.

CPU에서 실행되다가 I/O가 필요한 경우 해당 I/O 장치로 보내지는데, 이때는 `Device Queue`에서 대기한다. I/O 장치는 여러 개가 있으므로(키보드, 마우스 등등) 여러 대기열이 있는데, 
이들을 통칭해서 Device Queue라고 부른다. Device Queue에서 대기 중인 프로세스 가운데 어떤 것이 먼저 I/O를 받을 것인가를 결정하는 것이 `Device Scheduler`이다.

> Scheduler는 OS 내부에 존재하는 하나의 코드 영역, 즉 프로그램이다.

![Queues](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FepHCvj%2Fbtq2npDGmXb%2F8NefdQamTmdsq6GzYxsho0%2Fimg.png)

## Context Switching(문맥 전환)

현재 임의의 프로세서 P1이 CPU에서 실행되고 있다고 가정하자. Ready Queue에는 여러 다른 프로세스들이 대기하고 있다. 
P1에 할당된 시간이 다 끝나서 P1을 Ready Queue로 내리고 다음 프로세스를 올리려고 한다. 

CPU scheduler가 임의의 프로세서 P2를 다음 프로세스로 선택한다.
이 때, P1의 현재 정보를 PCB1에 저장하고, PCB2에 저장되어 있는 P2의 정보를 새로 로드해야한다. 이를 `dispatch`라고 하고 이 역할을 수행하는 프로그램을 `dispatcher`라고 한다. 
그리고 이렇게 실행되는 프로세스를 교체하는 것을 `context switching`이라고 한다.

PCB의 정보를 저장하고 로드하는데 드는 비용을 context switching overhead라고 한다.

![image](https://user-images.githubusercontent.com/63030569/115208127-33d80380-a137-11eb-881f-70a00d4ece49.png)

## 기타 용어들

`degree of multiprogramming`: 메모리에 올라와있는 프로세스의 수가 많을수록 degree가 높다고 말할 수 있다.

`I/O bounded process`: I/O의 빈도가 높은 프로세스, 이를테면 워드 프로세서.

`CPU bounded process`: CPU 사용 빈도가 높은 프로세스, 이를테면 기상 예측 프로세스처럼 연산량이 많은 것들.

> I/O bounded process가 메모리에 많이 올라와 있으면 I/O는 엄청 바쁜데 CPU는 놀고 있을 수 있다. 그 반대도 마찬가지. 
> 따라서 Job Scheduler는 두 종류의 프로세스의 균형이 적절히 유지되도록 Job Queue를 관리해야한다.

### 참고자료

* Operating System Concepts 10th edition
* 양희재 교수님 운영체제 공개 강의(http://www.kocw.net/home/search/kemView.do?kemId=978503)
