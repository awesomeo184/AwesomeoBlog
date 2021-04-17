---
title: '[정렬] 삽입정렬(Insertion Sort)'
date: 2021-03-28 14:04:27
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

# 기본 아이디어

카드 놀이를 할 때 손 안에 쥔 카드를 정렬하는 방법과 같다.

현재 손 안에 j 장의 카드가 있고 카드열 A[0]-A[j-1]은 정렬되어 있는 상태이다.

새로운 카드를 뽑는다. 이 카드를 key라고 하자. 이때 key를 A[j-1], A[j-2] ... A[0] 순으로
뒤에서부터 비교하면서 알맞은 자리에 카드를 삽입한다.

실행 과정은 아래 영상 참고

<iframe width="560" height="315" src="https://www.youtube.com/embed/OGzPmgsI-pQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# 삽입 정렬의 특징
* 구현이 쉽다.
* 제자리 정렬이다.
* 안정 정렬이다.
* 시간 복잡도가 O($$n^{2}$$)으로 느리다.

# 의사코드

```
Algorithm Insertion_Sort(A)
    input: unsorted array
    
    for j = 2 to A.length
        // 새로 뽑은 카드를 key로 저장하고 뒤에서부터 비교한다.
        key = A[j]
        i = j - 1
        
        // key가 A[i]보다 크다면 더이상 비교하지 않고 바로 반복문을 탈출한다.
        // A[0] - A[i]까지는 이미 정렬된 상태이기 때문.
        while i > 0 and key < A[i]
            // key보다 큰 값을 한 칸 뒤로 옮긴다.
            A[i+1] = A[i]
            // 뒤에서 앞으로 계속 진행한다.
            i = i - 1
        
        A[i + 1] = key
```

# 시간 복잡도

for-loop를 한번 돌 때마다 비교 연산의 횟수가 1, 2, 3, ..., n-1로 점차 증가한다.

따라서 배열의 길이 N에 대해 총 비교 연산의 횟수는 $$\frac{n(n-1))}{2}$$ 회.

스왑 연산의 횟수는 비교 연산의 횟수보다 같거나 작으므로 전체 시간 복잡도는 O($$n^{2}$$)이다.

# 파이썬 코드

```python
def insertion_sort(array):
    for i in range(1, len(array)):
        key = array[i]
        while i > 0 and array[i-1] > key:  
            array[i] = array[i-1]
            i -= 1
        array[i] = key
```

## 만약 내림차순으로 정렬해야 된다면?
마찬가지로 뒤에서 앞으로 정렬하되, A[i] < key 인 동안 A[i]를 한칸 뒤로 옮긴다.

```python
def insertion_sort(array):
    for i in range(1, len(array)):
        key = array[i]
        while i > 0 and array[i-1] < key:
            array[i] = array[i-1]
            i -= 1
        array[i] = key
```

