---
title: '[컴퓨터구조] CPU 성능, Clock Speed, CPI'
date: 2020-10-19 14:04:63
category: 'ComputerArchitecture'
thumbnail: { thumbnailSrc }
draft: false
---

# 컴퓨터의 성능이란?
'어떤 컴퓨터가 더 빠른가?' 혹은 '어떤 컴퓨터가 성능이 더 좋은가?'라는 질문은 무엇을 의미할까요? 
보통 속도가 빠르면 성능이 좋다고 생각할 수 있지만 항상 그런 것은 아닙니다.

여객기를 생각해보죠. 여객기 A는 여객기 B보다 속도가 3배 빠릅니다. 
하지만 여객기 A는 한번에 100명의 승객을 수용할 수 있는 반면 여객기 B는 한번에 400명의 승객을 수용할 수 있습니다. 
승객의 입장에서는 여객기 A가 좋겠지만 항공사 입장에서는 여객기 B가 더 좋을 것입니다. 이처럼 어떤 관점에서 평가하느냐에 따라 성능에 대한 평가가 달라집니다.

이와 마찬가지로 일반 개인 사용자의 입장에서는 하나의 작업을 개시하고 종료하는데 걸리는 시간이 빠른 것이 좋겠죠. 
이를 `응답시간(Response Time)`, 혹은 `실행시간(Execution Time)`이 빠르다고 합니다. 
그러나 구글의 데이터센터처럼 엄청나게 많은 일을 처리해야 하는 곳에서는 작업 하나하나를 처리하는 시간은 좀 느리더라도 한번에 여러 작업을 처리할 수 있어서 일정 시간동안 처리할 수 있는 양이 더 많은 것이 좋겠죠. 
이처럼 단위 시간동안 처리하는 작업의 양을 `처리량(Throughput)` 혹은 `대역폭(Bandwidth)`이라고 합니다. 여기서는 응답시간에 초점을 맞춰서 이야기를 해보겠습니다.

# 성능비

실행시간을 기준으로 성능은 다음과 같이 정의됩니다.

`Performance = 1 / Execution Time`

![performance](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzTzmh%2FbtqZoxS5pfY%2FVoQdfHGTyYmDzbqj9CtuI0%2Fimg.png)


이는 컴퓨터 X가 컴퓨터 Y보다 N배 빠르다, 혹은 N배 성능이 좋다는 말과 같습니다.


# Clock Speed & CPI (Clock Cycle per Instruction)

컴퓨터는 클럭 펄스에 맞춰 명령어를 처리하죠. 클럭이 한번 도는 동안 메모리에서 명령어를 가져와서, 해석하고, 처리합니다. 이와 관련해서 궁금하신분들은 아래의 동영상을 참고해보세요.

<iframe width="560" height="315" src="https://www.youtube.com/embed/FZGugFqdr60" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

클럭 펄스가 한번 '톡' 튀고 다음 클록 펄스가 튀기까지 CPU는 여러가지 일을 처리하는데, 이를 `클럭 사이클(Clock Cycle)`이라고 하고 클럭 사이클이 한번 도는데 걸리는 시간을 `클럭 주기(Clock Period)`라고 합니다. 클럭 사이클 한번에 1초가 걸린다면 클럭 주기는 1초인 것이지요.

클럭 속도(Clock Speed)는 클럭 주기의 역수입니다. 만약 클럭 주기가 0.5초이면 클럭 속도는 1/0.5 = 2 이고 단위는 헤르츠(Hz)로 표기합니다. 즉 클럭 속도는 2Hz이고 이는 1초에 클럭 사이클을 두 번 돈다는 것이죠.

이를 바탕으로 CPU의 실행시간을 어떻게 표현할 수 있을까요?

만약 프로그램 하나를 처리하는 데 클럭 사이클을 200번 돌아야하고, 클럭 사이클 한번을 도는데 0.5초가 필요하다면 이 프로그램을 실행하는데는 200 X 0.5 = 100초가 필요하겠네요. 클럭 속도와 클럭 주기는 역수 관계이므로 프로그램의 실행시간은 아래의 두 식으로 표현할 수 있겠네요.

`프로그램의 CPU 실행시간 = 프로그램의 CPU 클럭 사이클 수 X 클럭 사이클 시간`

`프로그램의 CPU 실행시간 = 프로그램의 CPU 클럭 사이클 수 / 클럭 속도`

프로그램 하나는 여려 종류의 명령어들로 구성되어 있습니다. 그러니까 실행시간은 프로그램의 명령어 수에 비례하겠네요. 이런 관점에서 실행 시간을 명령어 수에 명령어의 평균 실행 시간을 곱한 값으로 계산할 수 있습니다.

`CPU 클럭 사이클 수 = 명령어 수 X 명령어당 평균 클럭 사이클 수`

명령어는 종류마다 필요한 클럭 사이클 수가 다르므로  한 명령어에 필요한 사이클 수는 전체 클럭 사이클 수의 평균으로 표현합니다. 이렇게 명령어 하나를 처리하는데 필요한 클럭 사이클 수를 CPI(Clock Cycles per instruction)이라고 합니다. 명령어 수는 흔히 IC(Instruction Count)라고 표현합니다.

자 이제 우리는 고전적인 CPU 성능식이 무엇을 의미하는지 알 수 있습니다.

`CPU 시간 = IC X CPI / Clock Speed`
