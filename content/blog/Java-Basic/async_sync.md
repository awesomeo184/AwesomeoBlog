---
title: '동기와 비동기, 블로킹과 논블로킹'
date: 2021-06-26 13:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: false
---

흔히 동기/비동기와 블로킹/논블로킹 개념을 혼용하는 경우가 많다. 동기=블로킹, 비동기=논블로킹
이렇게 생각하는 경우가 많은데 이는 틀린 생각이다. 

동기/비동기와 블로킹/논블로킹은 서로 다른 관점의 이야기이다. **동기/비동기는 두 작업의 주체가
서로 작업의 시간을 맞추느냐 안 맞추느냐에 대한 개념**이고 **블로킹/논블로킹은 내가 직접 제어할 수 없는
대상을 상대하는 방법에 대한 개념**이다. 따라서 동기이면서 논블로킹인 작업, 그리고 비동기이면서 블로킹인 작업도 
개념적으론 가능하다.

# 동기/비동기

![image](https://user-images.githubusercontent.com/63030569/123517691-683ee180-d6dd-11eb-8cab-180ebcf1a9cb.png)

동기는 작업을 수행하는 두 주체가 서로 작업의 시간을 맞추는 것을 말한다. 

* A와 B가 시작 시간 또는 종료 시간이 일치
    - A, B 쓰레드가 동시에 작업을 시작(CyclicBarrier)
    - 메서드의 리턴시간(A)과 결과를 전달받는 시간(B)이 일치
* A의 종료 시간과 B의 시작 시간이 일치
    - sychronized
    - BlockingQueue

![image](https://user-images.githubusercontent.com/63030569/123517701-755bd080-d6dd-11eb-9219-0446bf37b873.png)

작업을 수행하는 두 주체가 서로 작업의 시간을 맞추지 않으면 비동기이다.

비동기 작업은 동기 작업보다 throughput이 높다. 하지만 프로그래밍하기 어렵다.

# 블로킹/논블로킹

![image](https://user-images.githubusercontent.com/63030569/123518590-87d80900-d6e1-11eb-84e9-cc66376f7b06.png)

어떤 대상에게 작업을 요청하고 그 결과값을 받을 때까지 다른 일을 하지 않고 기다리면 블로킹이다.

![image](https://user-images.githubusercontent.com/63030569/123518605-96262500-d6e1-11eb-9a09-ac287101e59a.png)

대상의 작업 완료 여부와 상관없이 계속 작업을 수행하면 논블로킹이다.


### 참고하면 좋을 자료

https://www.youtube.com/watch?v=HKlUvCv9hvA
