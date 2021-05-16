---
title: '[OS] 메모리 관리(1)'
date: 2021-05-14 16:44:63
category: OS
thumbnail: { thumbnailSrc }
draft: false
---

CPU 스케줄링과 더불어 OS의 중요한 업무 하나가 바로 **메모리 관리(Memory Management)**이다.
기술의 발전과 더불어 메모리의 용량이 빠르게 증가했지만 그 발전 속도만큼 메모리 사용량도 빠르게 증가했기 때문에
메모리를 얼마나 효율적으로 사용하는가는 컴퓨터 발전 역사에 있어서 항상 중요한 문제였다.

메모리를 효과적으로 사용하기 위한 방법에 대해 OS는 두 가지 측면에서 접근한다.
* 메모리 낭비 없애기
* 가상 메모리(Virtual Memory)

# 메모리 낭비 없애기

OS가 낭비되는 메모리를 줄이기 위해 어떤 방법을 이용하는지 알아보기 전에 우선 프로그램이 메모리에 적재되는 과정을 살펴보자.

## 프로그램이 메모리에 올라가는 과정

우선 메모리는 다음과 같이 주소(address)와 데이터(data)로 구성된다고 할 수 있다.

![image](https://user-images.githubusercontent.com/63030569/118260562-72ba6880-b4ed-11eb-9dd8-85eefcfa5927.png)

<br>

CPU가 필요한 주소를 보내면 메모리는 해당하는 주소의 데이터를 CPU로 보낸다. CPU가 연산한 값을 저장하기 위해 주소와 데이터를 함께 보내는 경우도
있으므로 데이터는 양방향으로 오고간다.

우리가 고수준 언어로 원천 파일(Source file)을 작성하면 컴파일러가 이를 목적 파일(Object file)로 컴파일한다.
목적 파일은 링커에 의해 다른 목적 파일과 합해져 실행 파일(Executable file)이 된다. 프로그램이 동작할 때 메모리에 올라오는 파일은
바로 이 실행 파일이다.

실행 파일이 메모리에 올라올 때, 몇 번지 주소에 로드되어야 할까? 이를 프로그래머가 결정할 수 있을까?

모든 컴퓨터에서 항상 똑같은 프로그램이 매일 똑같이 돌아간다면 프로그래머가 주소를 직접 결정할 수도 있을 것이다. 하지만 알다시피 그럴 일은 없다.
따라서 프로세스를 메모리에 로드하는 일은 OS가 담당하는데, 이때 `MMU`(Memory Management Unit)라는 하드웨어가 관여한다.

MMU는 CPU와 메모리 사이에서 주소를 재배치(relocate) 역할을 한다.

![image](https://user-images.githubusercontent.com/63030569/118263188-e8740380-b4f0-11eb-8f1f-2891fd071f99.png)

예를 들어 CPU에서 0번지 주소를 내면, 재배치 레지스터(relocation register)가 500을 더해주고, 실제 메모리는 CPU가 500번지 메모리를 요구한 것으로
받아들인다. 이때 CPU가 내는 주소를 논리 주소(logical memory), 재배치된 주소를 물리 주소(physical memory)라고 한다.

이렇게 주소를 재배치하면 프로그램 입장에서는 자신이 어디에 로드되는지 알 필요가 없다(사실 알 방법도 없다). 그냥 프로그램이
0번지부터 시작한다고 가정하고 동작하면 MMU가 알아서 이를 물리 주소로 재할당 해주기 때문이다.

## 동적 적재(Dynamic Loading)

프로그램이 실행될 때, 반드시 모든 코드가 메모리에 올라갈 필요는 없다.

우선 모든 루틴(routine)이 다 사용되는 것이 아니다. 대표적으로 예외처리가 있다.

```C
int main() 
{
    ...

    if (file == null)
    { ... }

    ...
```

예외 처리는 항상 발생하는 루틴이 아니기 때문에 처음부터 메모리에 올리는 것이 아니라, 꼭 필요할 때 동적으로 메모리에 적재하면
메모리를 더 적게 사용할 수 있다. 데이터도 그런 예가 있다. 배열이 그렇다. 이렇게 런타임에 필요한 부분을 그때그때 메모리에 올리는 것을
`동적 로딩`이라고 한다.

## 동적 연결(Dynamic Linking)

공통 라이브러리 루틴을 메모리에 중복으로 올리는 것은 낭비이다. 예를 들어 main1.exe와 main2.exe가 메모리에 올라간다고 쳐보자.
main1과 main2는 모두 printf() 함수를 사용한다. 동적 연결을 사용하지 않고 두 프로그램 모두 전체를 메모리에 올린다면 메모리가 낭비된다.

![image](https://user-images.githubusercontent.com/63030569/118265440-26bef200-b4f4-11eb-95eb-358dd9a185f5.png)

따라서 자주 사용되는 라이브러리 루틴은 메모리에 하나만 적재되고, 다른 프로그램은 루틴 연결(link)을 런타임까지 미루다가 런타임에
동적으로 연결하면 메모리를 아낄 수 있다.

이렇게 동적으로 연결되는 공통 라이브러리 루틴을 리눅스에서는 공유 라이브러리(shared library)라고 부르고, 윈도우즈에서는
동적 연결 라이브러리(Dynamic Linking Library) 줄여서 DLL이라고 부른다.

## 스와핑(Swapping)

메모리에 적재되어 있으나 현재 실행되지 않는 프로세스가 존재할 수 있다. 이런 프로세스는 지금 당장 메모리 공간을 차지하고 있을 필요가 없다.
이런 프로세스를 하드웨어에 잠시 내려놓았다가 프로세스가 다시 시작될 때 메모리에 올리는 것을 스와핑이라고 한다.


![image](https://user-images.githubusercontent.com/63030569/118266643-d9438480-b4f5-11eb-9331-fab1ddd0d0d2.png)

만약 내가 hwp를 실행시켜놓고 한동안 사용하지 않고 있다면, 이 프로세스가 메모리 공간을 차지하고 있는 것은 낭비다. 그래서 OS는 주기적으로 이런 프로세스를 파악해
하드 디스크로 프로세스를 쫓아낸다. 이를 스왑 아웃(swap-out)이라고 한다. 스왑 아웃된 프로세스가 저장되는 공간을 backing store 혹은 swap device라고 한다.
운영체제를 처음 하드에 설치할 때, 이 백킹 스토어의 크기를 직접 설정해줄 수 있다.

**백킹 스토어에 저장되는 프로세스 이미지는 하드에 저장된 hwp 실행 파일과는 다르다.** 실행 파일과 달리 스택, 사용 중이던 데이터 등도 함께 저장이 되어 있다. 이를 `프로세스 이미지`라고 부른다.
백킹 스토에이 있다가 다시 메모리로 올라가는 것을 스왑 인(swap-in)이라고 한다.

