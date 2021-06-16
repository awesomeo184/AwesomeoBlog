---
title: '[그래프] 다익스트라(Dijkstra) 알고리즘'
date: 2021-06-16 13:49:63
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

# 다익스트라 알고리즘

에지에 가중치가 있는 그래프(weighted graph)에서 최단 경로를 찾는 알고리즘

**에지의 가중치가 양수일때만 사용 가능하다.**

시작 노드 S로부터 다른 모든 노드까지의 최단 경로를 찾는다.

## 기본 원리

필요한 자료구조
* 노드 사이의 관계를 표현할 그래프: graph
* 시작 노드로부터 각 정점까지의 거리를 기록할 배열: distance
* 최단 경로가 확정된 노드를 꺼내올 최소힙: min_heap

<br>

1. 우선 시작 노드를 제외한 모든 노드의 비용을 가장 큰 값(이를테면, 무한대)으로 초기화한다.
2. 시작 노드를 최소힙에 넣는다.
3. 최소힙에서 노드를 꺼낸 후 인접한 노드를 확인한다. 이때,
   1. 인접한 노드로 이동하는 비용(현재까지의 비용+이동하는데 드는 비용)이 해당 노드에 기록되어있는 값보다 작다면? 값을 갱신한다.
   2. 아니라면, 최단 경로가 될 여지가 없으므로 그냥 넘어간다.
4. 3-1에서 거리가 갱신된 노드를 최소힙에 넣는다.
5. 최소힙의 크기가 0이 될 때까지 3~4번 과정을 반복한다.

<hr>

<br>

구체적인 예시로 살펴보자. 

`
1~2.시작 노드(0)를 제외한 모든 노드의 거리를 무한대로 초기화하고 시작 노드를 최소힙에 넣는다.
`
![image](https://user-images.githubusercontent.com/63030569/122169428-562c9a00-ceb8-11eb-95ec-98f32d58ae24.png)

<hr>

<br>

`3.최소 힙에서 노드를 꺼낸 후, 인접한 노드를 확인한다.`

노드 0 은 노드 1, 2, 3과 연결되어있고 각각의 비용은 8, 3, 10이다.

각 노드로 이동하는데 드는 비용은 각각 0+8, 0+3, 0+10이고 현재 기록된 무한대보다 작으므로 테이블을 갱신한다.

`4.그러고 난 후 갱신된 정보를 최소 힙에 넣는다.`
![image](https://user-images.githubusercontent.com/63030569/122171792-10bd9c00-cebb-11eb-8ed5-2501b6153399.png)

<hr>

<br>

`5.최소힙의 크기가 0이 될 때까지 3~4를 반복한다.`

최소힙에서 값을 꺼낸다. 노드 2는 3, 4와 연결되어 있고 각각의 비용은 6, 3이다.

2에서 3으로 이동하는데 드는 비용은 각각 3+6, 3+3이다. 현재 기록된 10, 무한대보다 작으므로 값을 갱신한 후
최소힙에 저장한다.

![image](https://user-images.githubusercontent.com/63030569/122173695-fd133500-cebc-11eb-9d77-ff6de6a7b756.png)

<hr>

<br>

계속 반복한다.

노드 4는 이동할 노드가 없으므로 넘어간다.

노드 1은 2, 4와 연결되어 있으나 각 노드로 이동하는 비용이 이미 기록된 값보다 크므로 그냥 넘어간다.

노드 3 역시 이동할 노드가 없으므로 넘어간다. 최소힙의 크기가 0이 되었으므로 종료한다.

![image](https://user-images.githubusercontent.com/63030569/122173598-d9e88580-cebc-11eb-8be4-4ec5bbd50473.png)


## 다익스트라 알고리즘의 정당성

세 번째 그림을 다시 보자

![image](https://user-images.githubusercontent.com/63030569/122173695-fd133500-cebc-11eb-9d77-ff6de6a7b756.png)

최소힙에서 노드 2를 꺼낸다.

최소 힙에서 값을 꺼낸다는 것은, 현재까지 저장된 거리 값 중, 가장 작은 거리 값을 가진 노드를 꺼낸다는 말이다.

현재 가장 작은 거리 값을 가진다는 사실은 **해당 거리가 최단 거리임을 보장한다.**

왜 최단 거리임이 보장될까?

역으로 노드 2의 현재 거리 값인 3이 최단 경로, 즉 최소값이 아니라고 생각해보자. 

이 말인 즉슨 다른 노드를 거쳐서 2번 노드로 가는 경로가 있고 이 때의 거리값이 3보다 작다는 말인데, 이는 성립하지 않는다.
왜냐하면 다른 노드로 가는 경로가 이미 3보다 크기때문에 다른 경로를 거쳐서 2번 노드에 도착했는데 거리 값이 3보다 작을 수는 없다.

값이 갱신되면 최소힙에 넣고, 최소힙의 크기가 0이 될 때까지 값을 꺼내면서 갱신하므로 결국에는 모두 최단 거리만 기록된다.

## 구현 코드(파이썬)

```python
import sys
import heapq

from collections import defaultdict
from typing import Dict, List, Tuple


def dijkstra(graph: Dict[int, List[Tuple[int, int]]], start: int):
    global distance

    # 최단거리가 확정된 노드를 꺼내기 위한 최소힙. 경로가 가장 작은 노드의 연결정보를 꺼내온다.
    # 튜플 내 순서가 (비용, 노드번호)인 이유는 나중에 heapq 에서 연산을 할때 튜플의 가장 첫번째 요소를 비교요소로 잡기 때문
    min_heap: List[Tuple[int, int]] = []

    # 시작노드에서 시작노드로 가는 비용은 0
    heapq.heappush(min_heap, (0, start))
    distance[start] = 0

    while len(min_heap) != 0:
        current_cost, current_node = heapq.heappop(min_heap)

        # 만약 현재 거리값이 기록된 거리값보다 크면 최단 거리일 여지가 없으므로 그냥 넘어간다.
        if distance[current_node] < current_cost:
            continue

        for adjacent_info in graph[current_node]:
            cost_to_adjacent_node: int = adjacent_info[0]
            adjacent_node: int = adjacent_info[1]

            # 현재 노드에서 인접한 노드로 이동할 때의 비용이 해당 노드의 현재 기록된 거리 값보다 작으면 값을 갱신한다.
            if distance[adjacent_node] > current_cost + cost_to_adjacent_node:
                distance[adjacent_node] = current_cost + cost_to_adjacent_node
                heapq.heappush(min_heap, (distance[adjacent_node], adjacent_node))


# input
# 첫 줄에 노드 수, 에지 수
# 둘째 줄부터 출발노드, 도착노드, 비용. 예를 들어 0, 1, 5가 주어지면 0번 노드에서 1번 노드로 이동할 수 있고 비용은 5이다.
'''
5 7
0 1 8
0 2 3
0 3 10
1 2 2
1 4 7
2 3 6
2 4 3
'''

num_of_node, num_of_edge = map(int, input().split())

# distance 배열의 모든 요소를 최댓값으로 초기화
distance: List[int] = [sys.maxsize for _ in range(num_of_node)]

# { 노드: 연결정보(비용, 노드번호)의 리스트 }
graph: Dict[int, List[Tuple[int, int]]] = defaultdict(list)

# 그래프 정보 입력
for _ in range(num_of_edge):
    from_, to_, cost = map(int, input().split())
    graph[from_].append((cost, to_))

dijkstra(graph, 0)

# 출력
for i in range(num_of_node):
    print(i, ":", distance[i])
```

## 시간 복잡도

다익스트라 알고리즘에서 시간 복잡도에 영향을 미치는 연산은 다음과 같다.

1. min_heap에서 꺼낸 노드와 인접한 노드를 탐색하는 연산
2. min_heap에 노드를 꺼내거나 삽입하는 연산

정점의 수를 N, 에지의 개수를 M이라고 할 때,  모든 에지는 한 번씩만 검사되기 때문에 1번 연산의 시간 복잡도는 O(M)이다.

2번 연산이 중요한데, 간선을 한번 방문할 때, 최악의 경우 매번 최소 힙에 값을 삽입한다.
heappush() 연산의 시간 복잡도는 O(logM)이다(최소힙에 추가될 수 있는 원소의 최대 개수는 간선의 개수이기 때문).
이를 총 M회 반복하므로 시간복잡도는 O(MlogM)이다.

만약 최소힙이나 우선순위 큐를 사용하지 않는 경우, distance 배열에서 직접 최소값을 찾아야하는데 이 경우 시간 복잡도는 O($$N^{2}+M$$)이다.

그래프가 복잡하지 않은 경우 최소힙을 사용하지 않는 것이 더 빠를 수 있다. 
