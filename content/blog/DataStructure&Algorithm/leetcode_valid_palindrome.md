---
title: '[리트코드] 125.Valid Palindrome '
date: 2021-06-25 14:45:63
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

https://leetcode.com/problems/valid-palindrome/submissions/

문자열 문제에서 흔히 등장하는 팰린드롬 문제이다.

처음 생각해낸 방법보다 더 빠르고 좋은 방법이 있어서 정리해둔다.

출처: [파이썬 알고리즘 인터뷰](http://book.interpark.com/product/BookDisplay.do?_method=detail&sc.prdNo=334277531&gclid=Cj0KCQjw2tCGBhCLARIsABJGmZ5F-HzOUFYnHpY7pHinGOmxfkhWm4qA-Vn-HwGHNJ2d6sQUNDTOEZYaAq0uEALw_wcB)

# 생각의 과정

가장 먼저 생각해볼 수 있는 방법은 문자열을 반대로 뒤집어서 같은지 확인하는 것이다.
문자열을 바로 뒤집는 법은 생각나지 않으므로 우선 문자열을 쪼개서 리스트로 만든다.

> 알아보니 문자열 슬라이싱을 이용해 문자열을 뒤집을 수 있다.
> 문자열을 다루는 문제는 코딩 테스트에서 자주 등장하므로 파이썬의 문자열 슬라이싱에 대해 따로
> 공부해놓는 것이 좋을 것 같다.

<br>

1. 리스트로 변환하기
   - 문자열을 순회하며 알파벳 혹은 숫자이면 lowercase로 바꿔서 빈 배열에 추가한다.
2. 해당 리스트를 뒤집은 리스트를 만든다
3. 두 리스트를 비교하며 요소와 순서가 같은지 확인한다.

# 의사코드 

```
Algorithm isValidPalindrome(targetString) -> boolean:
    arr = []

    for char in targetString:
        if char.isAlphaNumeric():
            arr.append(char.lowercase())

    reversedArr = arr.reverse()

    for i in range(len(arr)):
        if arr[i] != reversedArr[i]:
            return False
    return True
```

# 코드 구현

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        arr: List = []

        for char in s:
            if char.isalnum():
                arr.append(char.lower())

        reversedArr: List = list(reversed(arr))

        for i in range(len(arr)):
            if arr[i] != reversedArr[i]:
                return False

        return True
```

# 최적화

## Deque을 이용한 방법
위에서 작성한 코드는 원본 리스트를 뒤집은 리스트를 새로 생성한다. 따라서 O(n)만큼의 추가적인 메모리도 필요하고
시간 복잡도도 O(n)이다.

근데 문자열을 반으로 나누고, 앞부분과 뒷부분을 비교해도 팰린드롬인지 아닌지 알 수 있다.
즉 굳이 리스트를 새로 생성하지 않고 기존 리스트에서 앞에서 한 글자 뒤에서 한 글자씩 빼서 같은지 확인하면
O(n/2)으로 처리할 수 있다. 물론 점근적인 측면에서 n과 n/2는 차이가 없지만 일단 메모리를 추가적으로 사용하지
않아도 된다는 것은 확실하다.

List의 pop(index) 메서드를 사용해도 되지만 deque를 이용하면 훨씬 빠르다.

```python
def isPalindrome(self, s: str) -> bool:
   arr: Deque = collections.deque()

   for char in s:
      if char.isalnum():
         arr.append(char.lower())

   while len(arr) > 1:
      if arr.popleft() != arr.pop():
         return False

   return True
```

## 정규표현식과 슬라이싱을 이용한 방법

정규표현식으로 알파벳과 숫자만 골라내고 슬라이싱으로 문자열을 뒤집어서 같은지 비교한다.

가장 빠른 방법이다.

```python
def isPalindrome(self, s: str) -> bool:
   s = s.lower()
   
   s = re.sub('[^a-z0-9]', '', s)
   return s == s[::-1]
```