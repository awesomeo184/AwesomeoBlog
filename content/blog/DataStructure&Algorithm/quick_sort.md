---
title: '[정렬] 퀵 정렬(Quick Sort)'
date: 2021-04-17 15:04:27
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

앞에서 살펴본 `선택 정렬`, `삽입 정렬`, `버블 정렬`은 모두 구현은 간단하지만 느린 정렬 알고리즘이었다.
이번 글에서 살펴볼 퀵 정렬은 가장 많이 쓰이는 정렬 알고리즘이며, 평균적인 시간 복잡도가 O(NlogN)으로 상대적으로 빠른 정렬 알고리즘이다.

# 기본 아이디어

우선 배열 안에서 임의의 요소를 고른다. 이 요소를 피벗(pivot)이라고 부른다.

![image](https://user-images.githubusercontent.com/63030569/115117164-2eb07280-9fd8-11eb-87f6-97621635a156.png)

설명을 위해 중간에 위치한 원소를 피벗으로 잡았지만, 아무 원소나 잡아도 상관없다. 

이 피벗을 선택하는 방법에도 여러 알고리즘이 있다. 
가장 왼쪽의 요소를 피벗으로 삼는 방법도 있고, 랜덤으로 선택하는 방법도 있고, 또 가장 오른쪽의 요소를 피벗으로 삼는 방법도 있는데,
최종적인 성능은 모두 비슷하다.

<br/>
피벗을 잡았으면, 배열을 순회하면서 피벗보다 작은 쪽과 피벗보다 큰 쪽으로 분류한다.

![image](https://user-images.githubusercontent.com/63030569/115117307-d037c400-9fd8-11eb-9b01-679325cce7ca.png)

<br/>

그리고 왼쪽과 오른쪽 배열을 같은 방식으로 정렬한다.

![image](https://user-images.githubusercontent.com/63030569/115117429-797eba00-9fd9-11eb-94e7-586794d2e296.png)


# 파이썬 코드

위 과정을 코드로 표현해보면 다음과 같다.

```python{9}
def quick_sort(A):

    if len(A) <= 1:
        return A

    # 피봇은 가장 왼쪽 요소로 한다.
    pivot = A[0]

    L, M, R = [], [], []

    for x in A:
        if pivot > x:
            L.append(x)
        elif pivot < x:
            R.append(x)
        else:
            M.append(x)

    # 왼쪽 배열과 오른쪽 배열에 재귀적으로 퀵 정렬을 호출하고
    # 세 개의 배열을 연결한다(concatenate).
    return quick_sort(L) + M + quick_sort(R)
```

이 방법은 굉장히 직관적이고 이해하기 쉽지만 한 가지 문제점이 있다. 강조 표시한 10번째 라인을 보면 새로운
리스트를 생성해서 값들을 담는데, 이는 추가적인 메모리 공간을 요구한다. 이런 방식을 `제자리 정렬(in-place)이 아니다`라고 말한다.
제자리 정렬이란, 추가적으로 배열을 복사하지 않고, 원래의 배열에서 자리바꿈하면서 배열을 정렬하는 것을 말한다.
앞서 살펴본 선택, 삽입, 버블 정렬이 대표적인 제자리 정렬 알고리즘이다.

(위 방법은 제자리 정렬은 아니지만, 앞에서부터 순차적으로 배열에 집어넣기 때문에 안정 정렬이다.) 

퀵 정렬도 제자리 정렬로 구현할 수 있는데(이 경우 불안정 정렬이다.), 조금 복잡하지만 천천히 따라가보면 그리 어렵지 않다.

우선 정렬할 배열의 범위를 가리키는 변수, first와 last를 지정한다. 그리고 가장 첫번째 요소를 피봇으로 선택한다(다시 말하지만 피봇을 고르는 기준은 여러가지가 있다).
그리고 피봇의 바로 다음 인덱스를 left, 마지막 인덱스를 right으로 지정한다.

![image](https://user-images.githubusercontent.com/63030569/115118961-f7928f00-9fe0-11eb-9ba8-ce6ebeae4971.png)

(1) pivot보다 큰 값이 나올 때까지 left를 오른쪽으로 이동시킨다. 그 후 (2) pivot보다 작은 값이 나올 때까지 right를 왼쪽으로 이동시킨다.

그러고 나서 (3) left가 가리키는 값과 right가 가리키는 값을 서로 바꾼다. (4) 값을 바꾸고 나면, left는 한 칸 오른쪽으로, right는 한 칸 왼쪽으로 옮긴다.

![image](https://user-images.githubusercontent.com/63030569/115119317-b0a59900-9fe2-11eb-89db-6c4953d0e0e6.png)

언제까지 이 과정을 반복해야 할까? 바로 left와 right가 엇갈리는 순간까지, 즉 left <= right인 동안 위 과정을 반복해야 한다.

![image](https://user-images.githubusercontent.com/63030569/115119577-16465500-9fe4-11eb-88a8-9bb008fac7f9.png)

지금까지의 과정을 코드로 나타내보면 다음과 같다.

```python{17}
def quick_sort(A, first, last):
    pivot = A[first]
    left = first + 1
    right = last
    
    # left와 right가 엇갈릴 때까지 반복한다. left == right인 경우에도 인덱스를 이동시켜야 엇갈린다.
    while left <= right:
    
        # left가 가리키는 값이 pivot보다 작다면 계속해서 left를 오른쪽으로 옮긴다.
        # 이 때, pivot이 배열에서 가장 큰 값이라면 인덱스가 계속 이동해서 배열의 범위를 넘어설 수 있다.
        # 그러므로 left <= last 조건을 추가한다.
        while left <= last and pivot > A[left]:
            left += 1
        
        # 위 반복문을 탈출했다는 것은 left의 이동이 멈췄다는 뜻이므로
        # 이번엔 pivot보다 작은 값이 나올 때까지 right를 계속 왼쪽으로 이동시킨다.
        while pivot < A[right]:
            right -= 1
        
        # 아직 엇갈리지 않은 상태라면
        if left <= right:
            # 자리바꾸고
            A[left], A[right] = A[right], A[left]
            # 위치를 한번씩 더 이동
            left += 1
            right -= 1 
```

이때, 17번 라인에서 right가 계속 줄어들어서 배열의 범위를 벗어나면 어떡하냐라는 생각을 할 수 있는데,
right는 배열의 범위를 넘어설 일이 없다. 왜냐하면 pivot 오른쪽의 모든 값들이 pivot보다 크더라도
right가 pivot을 가리키는 순간 반복문을 탈출하게 된다(`[4,5,6,7,8]` 인 경우 right이 언제 멈출지 생각해보라!)


left와 right가 엇갈려서 모든 반복문을 탈출하면 어떻게 하면될까? 단순히 right과 pivot의 위치를 바꿔주면된다.

![image](https://user-images.githubusercontent.com/63030569/115120472-aab2b680-9fe8-11eb-9bbf-320a1aa02423.png)

```python
A[first], A[right] = A[right], A[first]
```

그리고 pivot을 기준으로 나눠진 배열에 대해 재귀적으로 quick_sort를 호출하면 배열이 정렬된다.

```python
# 왼쪽 배열에 대해 퀵정렬 호출
quick_sort(A, first, right-1)

# 오른쪽 배열에 대해 퀵정렬 호출
quick_sort(A, left, last)
```

재귀 호출을 할때는 반드시 base case가 있어야 하는데, 위 코드에는 base case가 없다.
재귀는 언제 탈출해야될까?  

첫번째 재귀호출을 하는 부분을 보면, 매개변수 last에 해당하는 right-1은
계속 줄어들다가 언젠가 first에 전달되는 0보다 작아질 것이다.

또 두번째 재귀호출 하는 부분은, first에 해당하는 left가 계속 증가하면서 언젠가 last에 해당하는
배열의 마지막 인덱스보다 커진다. 이 경우 각각 last와 first가 배열의 범위를 벗어나므로 코드의 진행을 중단해야한다.
그러므로 first >= last 일 때를 base case로 잡아주면된다.

최종 코드는 다음과 같다.

```python
def quick_sort(A, first, last):
    if first >= last:
        return

    pivot = A[first]
    left = first + 1
    right = last

    while left <= right:

        while left <= last and pivot > A[left]:
            left += 1

        while pivot < A[right]:
            right -= 1

        if left <= right:
            A[left], A[right] = A[right], A[left]
            left += 1
            right -= 1

    A[first], A[right] = A[right], A[first]

    quick_sort(A, first, right - 1)
    quick_sort(A, left, last)


arr = [3, 1, 2, 5, 4, 7, 6]
quick_sort(arr, 0, len(arr) -1)
print(arr) # [1, 2, 3, 4, 5, 6, 7]
```

# 시간 복잡도

퀵 정렬의 실행 시간을 T(n)이라고 하면 T(n)은 다음과 같이 표현될 수 있다.

![image](https://user-images.githubusercontent.com/63030569/115121512-1e0af700-9fee-11eb-9dfe-aabc4da3b4f5.png)

pivot을 기준으로 배열을 좌,우로 분리하기 위해서는 배열 전체를 순회하며 n-1회의 비교연산과 스왑연산을 하므로 이때 시간 복잡도는 cn이다.
그리고 왼쪽 배열과 오른쪽 배열에 대해 각각 다시 재귀적으로 퀵 정렬을 수행하므로 T(L)과 T(R)을 더해줘야 한다.

worst case는 피봇이 배열의 최댓값 혹은 최솟값이어서 모든 원소가 L이나 R 한 쪽으로 몰리는 경우다. 이 때, 나머지 배열의 원소는 pivot을
제외한 n-1개 이므로 시간 복잡도는 다음과 같이 표현된다.

T(1) = c

T(n) = T(n-1) + cn

위 형태의 점화식을 풀면 시간복잡도는 O($$n^{2}$$)이다.

best case는 pivot을 기준으로 원소가 고르게 좌, 우로 나눠지는 경우이다. 이때 각 배열의 원소는 $$\frac{n}{2}$$개이므로
수행 시간은 다음과 같이 표현된다.

T(n) = T( $$\frac{n}{2}$$ ) + T($$\frac{n}{2}$$) + cn 

→ T(n) = 2 * T($$\frac{n}{2}$$) + cn

위 형태의 점화식을 풀면 시간 복잡도는 O(NlogN)이다.

worst case는 입력 배열이 오름차순 혹은 내림차순으로 정렬된 경우인데, 이런 경우는 흔치 않으므로
평균적인 시간 복잡도는 O(NlogN)이라고 말할 수 있다.