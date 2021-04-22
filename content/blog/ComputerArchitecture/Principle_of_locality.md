---
title: '[컴퓨터구조] CPU 성능, Clock Speed, CPI'
date: 2020-11-13 13:04:63
category: 'ComputerArchitecture'
thumbnail: { thumbnailSrc }
draft: false
---

# 지역성의 원칙(Principal of Locality)

컴퓨터의 메모리 시스템을 이해하기 위해 필수적으로 이해해야 하는 개념이 바로 지역성의 원칙이다.

메모리 시스템을 설명하는데 가장 많이 이용되는 것이 도서관의 비유이다. 도서관은 컴퓨터의 하드 디스크와 같다. 수많은 책이 있지만 원하는 책을 찾으려면 시간이 오래 걸린다.
나는 컴퓨터의 역사에 관한 레포트를 쓰기 위해 도서관에 왔다. 레포트를 쓰기 위해서 나는 우선 컴퓨터 분야 서가에 가서 필요할만한 책 몇권을 가지고 내 자리로 돌아올 것이다.
나는 책상 위에 책을 쌓아놓고 수시로 그 책들을 참조할 것이다. 이때 책상은 컴퓨터의 메인 메모리, 혹은 캐시 메모리와 비슷하다. 책은 몇 권 없지만 빠르게 참조할 수 있다.

도서관에서 책을 찾는 방법과 프로그램이 동작하는 방법에는 지역성의 원칙이 똑같이 적용된다. 어떤 순간에 사용하는 책은 도서관에 있는 책의 극히 일부이듯이 **프로그램은 어떤 특정 시간에 메모리 공간의 비교적 작은 부분에만 접근한다는 것이 지역성의 원칙**이다.
지역성의 원칙에는 시간적 지역성과 공간적 지역성 두 가지가 있다.

## 시간적 지역성(Temporal Locality)
한 번 쓰인 것은 얼마안가 또 쓰일 가능성이 높다는 것이 시간적 지역성이다. 즉 내가 어떤 정보를 찾기 위해서 책들을 책상으로 가져왔다면, 곧바로 그 책을 다시 찾아보게 될 가능성이 크다.

## 공간적 지역성(Spatial Locality)
어떤 항목이 참조되면 그 근처의 항목들이 곧바로 참조될 가능성이 높다. 예를 들어 내가 컴퓨터의 역사에 대한 정보를 얻기 위해 한 곳에서 책을 가져왔다면 그 책의 주변에 비슷한 주제의 책을 곧 참조할 가능성이 높아진다.

다음과 같은 C 프로그램이 있다고 하자.
```C
int sum = 0;
for (int i=0; i<100; i++)
{
sum = sum + arr[i];
}
```

이 프로그램에서 시간적 지역성 혹은 공간적 지역성이 이용되는 부분은 어디일까?

변수 `i` 와 `sum` 은 루프를 돌 때마다 반복적으로 접근된다. 따라서 CPU는 이 두 변수의 주소에 빠르게 접근할 수 있도록 가까운 곳에 두어 시간적 지역성을 이용해야 할 것이다.

루프를 돌 때마다 `arr[0]`, `arr[1]`, `arr[2]` ... 이렇게 배열의 각 요소에 순차적으로 한 번씩 접근한다. 따라서 CPU는 이 배열의 시작점이 저장된 주소와 그 주변의 주소들을 가까운 곳에 가져와서 공간적 지역성을 이용해야 할 것이다.

# 메모리 계층 구조(Memory Hierarchy)

컴퓨터의 메모리를 메모리 계층 구조(Memory Hierarchy)로 구현함으로써 이런 지역성의 원칙을 이용할 수 있다. 크기가 작고 속도가 빠른 메모리는 CPU와 가깝게 두고, 크기가 크고 느린 메모리를 그 아래 두어 계층 구조를 구현할 수 있다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F5MhD7%2FbtqRL4qjsHx%2F0UkUQmaZyqkUgFJY7Khk71%2Fimg.png)

<br/>

위로 갈수록 CPU와 가깝고, 크기가 작고, 속도가 빠르며, 비싸다. 아래로 갈수록 CPU와 멀고, 크기가 크며, 싸다.

속도가 빠른 메모리를 크게 만들면 되지 않냐고 생각할 수 있지만, 속도가 빠를수록 단위 비트당 생산 가격이 높다. 컴퓨터 메모리 설계의 3가지 목표는 large capacity, high speed, low cost인데, 지역성을 활용해 메모리를 계층적으로 구현하면 이 세 가지 목표를 효율적으로 달성할 수 있다.

CPU가 요구한 데이터가 상위 계층에 있으면 이를 적중(hit)이라고 부르고 없으면 실패(miss)라고 부른다. 상위 계층에 있는 데이터는 하위 계층에 있는 데이터의 일부를 복사해서 적재한 것이다. 따라서 상위 계층에 있는 데이터는 반드시 하위 계층에도 존재하며, 하위 계층에 없는 데이터가 상위 계층에 존재할 수는 없다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FuBN98%2FbtqN8Y0Mkxy%2FcXKfipGodpdrsoJKdukme0%2Fimg.jpg)

메모리 계층 구조는 가장 최근에 접근한 데이터를 CPU 가까이 적재함으로써 시간적 지역성을 이용한다. 이때, 그 데이터 하나만 가지고 오는 것이 아니라, 그 주변에 있는 데이터까지 한번에 퍼올려서 적재함으로써 공간적 지역성을 이용한다. 한번에 퍼올리는 데이터의 단위를 블록(block) 혹은 라인(line)이라고 부른다.

### 참고자료

Computer Organization and Design 5th edition