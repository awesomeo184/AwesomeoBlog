---
title: '[정렬] 정렬 알고리즘에서 안정성(Stability)란?'
date: 2021-02-15 18:26:27
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

해당 글은 [이 글](https://www.geeksforgeeks.org/stability-in-sorting-algorithms/)을 번역 및 요약한 것입니다.

# 안정성이란? 

정렬 알고리즘에서 말하는 안정성(stability)란 키-값 쌍을 가진 객체들 중 같은 키를 가진 객체들의 순서가 정렬 이후에도 유지되는 것을 말합니다.

![https://www.geeksforgeeks.org/stability-in-sorting-algorithms/](https://www.geeksforgeeks.org/wp-content/uploads/stability-sorting.jpg)

위 예시에서 각 공들은 숫자와 색깔을 가지고 있습니다. 숫자를 기준으로 공을 정렬했지만, 색깔의 순서(녹색 앞에 분홍색이 온다)는 그대로 유지되었기 때문에 이것은 안정적인 정렬(stable sort)입니다.

<br/>
그럼 안정 정렬은 언제 의미가 있을까요?

단순히 key로만 이루어진 배열에서는 안정성을 고려할 필요가 없습니다. 예를 들어 아래와 같은 정수 배열은 안정 정렬을 사용하던 불안정 정렬을 사용하던 상관이 없습니다.

```python
[1, 5, 3, 4, 2, 4, 5]
```

**정렬의 안정성은 `키-값 쌍`으로 이루어진 요소들을 정렬할 때 고려되어야 합니다.**

학생의 이름과 반의 정보를 저장한 배열이 있다고 가정해봅시다.
```python{2, 4}
(Dave, A)
(Alice, B) 
(Ken, A)
(Eric, B)
(Carol, A)
```
<br />

오로지 이름을 기준으로 정렬한다면 결과는 다음과 같습니다.

```python{1, 4}
(Alice, B)
(Carol, A)
(Dave, A)
(Eric, B)
(Ken, A)
```

<br />
A반 학생들이 앞으로 오도록 다시 정렬하고 싶다고 합시다. 불안정 정렬을 사용한다면, 정렬 이전의 순서는 보장되지 않기 때문에 아래와 같이 정렬될 수도 있습니다.

```python{4, 5}
(Carol, A)
(Dave, A)
(Ken, A)
(Eric, B)
(Alice, B)
```

정렬 전에는 Alice가 Eric 앞에 있었는데, 정렬 이후에 그 순서가 깨져버렸습니다. 
안정적인 정렬을 사용한다면, 반을 기준으로 다시 정렬하더라도 이전에 이름으로 정렬되었던 순서를 유지할 수 있습니다.

```python{4, 5}
(Carol, A)
(Dave, A)
(Ken, A)
(Alice, B)
(Eric, B)
```
<br />

`버블 정렬`, `삽입 정렬`, `병합 정렬`, `계수 정렬` 등은 기본적으로 `안정적인 정렬`입니다.

`선택 정렬`, `퀵 정렬`, `힙 정렬` 등은 기본적으로 `불안정적인 정렬`입니다.


