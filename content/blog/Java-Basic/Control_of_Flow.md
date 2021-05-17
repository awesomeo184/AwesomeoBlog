---
title: '[Java Study 4주차] 자바 제어문'
date: 2021-1-2 13:13:27
category: 'Java-Basic'
thumbnail: { thumbnailSrc }
draft: true
---

> ** 본 포스팅은 백기선님이 진행하시는 [자바 스터디](https://github.com/whiteship/live-study)를 진행하며 혼자 공부하고 이해한 내용을 바탕으로
> 정리한 글입니다. 오류나 지적 사항이 있다면 댓글로 알려주시면 감사하겠습니다.

* 목표: 자바가 제공하는 제어문을 학습하세요.

# 제어문

프로그램의 실행 제어에는 세 가지 구조가 있다.

![699451EF-0808-4A1D-81F2-C2643711179C](https://user-images.githubusercontent.com/63030569/115996744-89bb1880-a61b-11eb-975c-0c950e5b9bdc.png)

순차 구조에서 벗어나 프로그래머가 원하는 실행 흐름대로
프로그램을 제어하기 위한 문장(statement)을 제어문이라고 한다. 그 중 선택 구조와 반복 구조를 위해 사용되는 문장을 각각 선택문, 제어문이라고 한다.

## 선택문

자바의 선택문에는 `if-else` 예약어를 사용하는 `if문`과 `switch-case` 예약어를 사용하는 `switch문`이 있다.

### if 문

