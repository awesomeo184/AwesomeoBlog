---
title: '[정렬] 버블정렬(Bubble Sort)'
date: 2021-03-11 20:04:63
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

# 기본 아이디어

마치 파도의 거품이 뒤로 밀리듯이 정렬을 한다고 하여 붙은 이름.

배열을 순회하면서 인접한 두 원소를 서로 비교한 후, 앞의 원소가 더 크다면 서로 위치를 바꾼다(오름차순일 때)

![버블정렬](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbUriug%2Fbtq090GyQBk%2FYU1pmyrtM6gypU0kvrQ3dk%2Fimg.gif)

만약 마지막까지 도달했는데 스왑이 한번도 일어나지 않았다면, 모든 원소가 정렬 상태에 도달한 것으로 판단할 수 있다.
이때는 연산이 끝나지 않았음에도 강제로 연산을 종료함으로써 수행시간을 조금 더 줄일 수 있다.

# 버블정렬의 특징
* 구현이 쉽다.
* 제자리 정렬이다.
* 안정 정렬이다.
* 시간복잡도가 O($$n^{2}$$)으로 느리다.
* 스왑 연산이 많이 일어나기 때문에 비효율적이다.

# 의사코드

```
Algorithm Bubble_Sort(A)
    input: unsorted array
    
    for i = A.length - 1 to 2:
        for j = 1 to i - 1:
            // 앞에 있는 요소가 더 크면
            if A[j] > A[j + 1]:
                //뒤에 있는 요소와 자리바꿈
                tmp = A[j]
                A[j] = A[j + 1]
                A[j + 1] = tmp
```

기본적인 알고리즘은 이러하지만, 정렬이 된 상태인지 아닌지 확인하는 flag를 추가해줌으로써
실행 시간을 조금 더 단축할 수 있다.

```
Algorithm Bubble_Sort(A)
    input: unsorted array
    
    for i = A.length - 1 to 2:
        //flag
        has_sorted = False
        
        for j = 1 to i - 1:
            // 앞에 있는 요소가 더 크면
            if A[j] > A[j + 1]:
                //뒤에 있는 요소와 자리바꿈
                tmp = A[j]
                A[j] = A[j + 1]
                A[j + 1] = tmp
                
                // 스왑이 일어났다면 변수를 True로 바꾼다.
                has_sorted = True
        
        //스왑이 한번도 안 일어났다면 이미 정렬이 되어 있는 것이므로 반복문 탈출
        if not has_sorted:
            break
```

# 시간 복잡도

외부 루프가 한번 돌 때마다 내부 루프에서 비교연산이 처음에는 n-1회, 다음에는 n-2회 ... 2회, 1회로 줄어든다.
외부 루프는 n-1회 반복되므로 총 비교연산은

n-1 + n-2 + ... + 2 + 1 = $$\frac{n(n-1)}{2}$$회 일어나고,

스왑 연산 또한 최악의 경우 $$\frac{n(n-1)}{2}$$회이므로 전체적인 시간 복잡도는 O($$n^{2}$$)이다.

# 파이썬 코드

```python
def bubble_sort(array):
    for i in range(len(array) -1, 0, -1):
        has_sorted = False
        for j in range(i):
            if array[j] > array[j + 1]:
                array[j], array[j + 1] = array[j + 1], array[j]
                has_sorted = True
        if not has_sorted:
            break
```

