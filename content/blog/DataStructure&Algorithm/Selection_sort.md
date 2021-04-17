---
title: '[정렬] 선택정렬(Selection Sort)'
date: 2021-03-26 15:04:27
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

# 기본 아이디어

1. 배열을 순회하며 최소값을 찾는다. 최소값을 찾으면 배열의 가장 첫 인덱스에 있는 값과 자리를 바꾼다. (최대값을 찾을 수도 있다. 이때는 가장 뒤에 있는 값과 자리를 바꾼다.)
2. 다시 배열을 순회하며 다음 최소값을 찾는다. 최소값을 찾으면 두 번째 인덱스에 있는 값과 자리를 바꾼다.
3. 원소가 하나 남을 때까지 위 과정을 반복한다.

원소가 들어갈 자리는 이미 정해져 있고, 그 자리에 넣을 원소를 **선택**하는 알고리즘

![https://dev-lagom.tistory.com/37](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdN9qWS%2Fbtq08ugsFws%2F96VChAgktBddb3jdHf9Hzk%2Fimg.gif)

# 선택 정렬의 특징

* 가장 기본적인 정렬 알고리즘이며 구현이 쉽다.
* 제자리 정렬 알고리즘이다.
* 불안정 정렬 알고리즘이다.
* 시간 복잡도가 O($$n^{2}$$)으로 상당히 느리다.

# 의사코드

```
Algorithm Selection_Sort(A):
    input: array
    
    for i = 1 to A.lenth:
        //find index of min value
        min_index = i
        min_value = A[i]
        for j = i + 1 to A.length:
            if A[j] < min_value:
                min_index = j
                min_value = A[j]
        //swap A[i] and A[min_index]
        tmp = A[i]
        A[i] = A[min_index]
        A[min_index] = tmp
```

# 시간 복잡도

외부 루프가 n-1회 도는 동안 각 루프마다 비교 연산이 n-1, n-2, ... , 2, 1회 일어난다.
결과적으로 총 비교 연산은 $$\frac{n(n-1))}{2}$$회 일어난다.

스왑 연산은 최악의 경우 $$\frac{n(n-1))}{2}$$회 일어나므로, 전체 시간 복잡도는 O($$n^{2}$$)이다.

# 파이썬 코드

```python
def selection_sort(array):
    for i in range(len(array)):
        min_index = i
        min_value = array[i]

        for j in range(i+1, len(array)):
            if array[j] < min_value:
                min_index = j
                min_value = array[j]

        array[i], array[min_index] = array[min_index], array[i]
```