---
title: '[그래프] 프림 알고리즘'
date: 2021-06-16 21:49:63
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

# Prim's Algorithm

최소 신장 트리를 구성하는 알고리즘 중 하나이며, 최소 신장 트리의 Cut property를 이용하는 그리디 패러다임의 알고리즘이다.

Cut property만 이해 했다면 기본 아이디어는 굉장히 쉽다. 근데 코드로 구현하는게 개인적으로 조금 까다로웠다.

([Cut Property](https://awesomeo184.netlify.app/DataStructure&Algorithm/minimum_spanning_tree/)와 [다익스트라 알고리즘](https://awesomeo184.netlify.app/DataStructure&Algorithm/Dijkstra/)에 대한 내용은 링크 참조.)


인접리스트와 우선순위 큐를 사용하는게 구현이 쉽다. 본 포스팅에서도 이 방법을 사용했다. 

인접 행렬과 1차원 배열로 구현하는 방법에 대해서도 여러 코드를 찾아봤는데 너무너무 헷갈린다...

인접 행렬과 1차원 배열로 구현하는 방법이 궁금한 사람들은 [geeksforgeeks](https://www.geeksforgeeks.org/prims-minimum-spanning-tree-mst-greedy-algo-5/) 나 [이 강의](https://www.youtube.com/watch?v=jQH2wrz04io)를 참고해보면 좋을 것 같다.


# 기본 아이디어

노드의 개수가 N인 임의의 그래프 G가 있을 때, 

1. 임의의 노드를 선택한 후, G를 해당 노드를 포함하는 그래프인 T와 (G-T)로 나눈다.
2. Cut 에지 중에서 비용이 최소인 에지를 찾는다.
3. 찾은 에지에 연결된 두 노드 중 T에 포함되지 않은 노드를 T에 포함시킨다.
4. T의 에지의 개수가 N-1이 될 때까지 반복한다.

<br>

아래와 같은 그래프가 있을 때

![image](https://user-images.githubusercontent.com/63030569/122227644-08cd1e80-cef2-11eb-816b-906aa4b4b69f.png)

<br>
<hr>

임의의 노드 하나를 선택한 후 T에 포함시킨다(여기선 a를 선택했고 T에 포함된 노드와 간선은 파란색으로 표시).

그리고 T와 T-G 사이의 Cut 중에 비용이 가장 작은 에지를 찾는다.

![image](https://user-images.githubusercontent.com/63030569/122228098-7f6a1c00-cef2-11eb-9941-2f0e17c33bef.png)

<br>
<hr>

최소 비용 에지는 3이므로 이를 선택하고 해당 에지와 연결된 노드 중에서 T에 포함되지 않은 노드를 T에 포함한다.

이 과정을 계속 반복하면 최소 신장 트리가 완성된다.

![image](https://user-images.githubusercontent.com/63030569/122228374-bd674000-cef2-11eb-8ef2-63031d337aaf.png)

<br>
<hr>


![image](https://user-images.githubusercontent.com/63030569/122228703-133be800-cef3-11eb-834b-f755c4efb0a6.png)

![image](https://user-images.githubusercontent.com/63030569/122229032-5e55fb00-cef3-11eb-9f82-dc2c212a3271.png)

![image](https://user-images.githubusercontent.com/63030569/122228762-218a0400-cef3-11eb-8bf8-c4648e688a57.png)

![image](https://user-images.githubusercontent.com/63030569/122228804-2d75c600-cef3-11eb-8ce2-8f032a57545c.png)

# 구현 코드(파이썬)

```python
from queue import PriorityQueue


def prim(adj, start):
    # T에 포함된 정점을 표시하기 위한 변수
    covered = set()

    # prim 알고리즘의 결과 그래프를 리턴하기 위한 변수
    res_edges = []

    # 후보 간선을 관리하기 위한 우선 순위 큐, 후보간선(가중치, 목적 정점, 시작 정점)
    queue = PriorityQueue()
    queue.put((0, start, None))

    while not queue.empty():
        # 최소 가중치를 가지는 간선 정보
        u_cost, u, u_src = queue.get()

        # 이미 T에 포함되어 있다면 넘어간다
        if u in covered:
            continue

        covered.add(u)

        # u_src가 None인 경우는 간선이 아닌 첫번째 큐에 넣은 특별한 값이므로 무시
        if u_src is not None:
            res_edges.append((u_cost, u, u_src))

        # 정점 u와 연결된 모든 간선 (u,v) 중 v가 T에 포함되지 않은 경우에만 후보 간선에 넣어준다.
        for v_cost, v in adj[u]:
            if v not in covered:
                queue.put((v_cost, v, u))

    return res_edges


graph = {
    "a": [(4, "b"), (3, "c")],
    "b": [(4, "a"), (2, "c"), (7, "d")],
    "c": [(3, "a"), (2, "b"), (5, "d"), (11, "e")],
    "d": [(7, "b"), (5, "c"), (2, "e"), (8, "f")],
    "e": [(11, "c"), (2, "d"), (9, "f")],
    "f": [(8, "d"), (9, "e")]
}

res_edges = prim(graph, "a")

for u_cost, u, v in res_edges:
    print(u, " - ", v, " : ", u_cost)
```

출력결과

```
c  -  a  :  3
b  -  c  :  2
d  -  c  :  5
e  -  d  :  2
f  -  d  :  8
```