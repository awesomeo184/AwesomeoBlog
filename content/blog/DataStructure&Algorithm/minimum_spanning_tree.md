---
title: '[그래프] 최소 신장 트리(MST)'
date: 2021-06-16 18:49:63
category: 'DataStructure&Algorithm'
thumbnail: { thumbnailSrc }
draft: false
---

# 최소 신장 트리(Minimum Spanning Tree)

신장 트리(spanning)란? : 그래프 G를 구성하는 부그래프로서 G의 모든 정점을 포함하는 그래프.

최소 신장 트리란, 이러한 신장 트리들 중에서 에지의 가중치의 합이 최소인 신장트리를 말한다.

![image](https://user-images.githubusercontent.com/63030569/122211278-84729f80-cee1-11eb-9edb-4ab53951b903.png)

파란색으로 표시된 에지로 연결된 노드로 구성된 그래프가 위 그래프의 최소 신장 트리이다.


대표적인 응용 문제로는 각 도시를 연결하는 도로를 짓는데 드는 최소 비용 구하기 등이 있다.

# 최소 신장 트리의 성질

최소 신장 트리의 중요한 특징 세 가지.

## 1.트리성질

최소 신장 트리는 트리이기 때문에 당연히 트리가 가지는 성질을 똑같이 갖는다. 트리가 갖는 성질에는 다음과 같은 것이 있다.

* 노드의 수가 N개이면 에지의 수는 (N-1)개이다.
* 에지 하나를 삭제하면 두 개의 부트리가 생성된다.
* 새로운 에지를 삽입하면 그 에지를 포함하는 사이클이 생성된다.

<br>

## Cut property

Cut이란 에지의 부분 집합인데, Cut에 해당하는 에지를 삭제하면 두 개 혹은 그 이상의 분리된 그래프가 생성된다.

보통 곡선으로 나타낸다.

![image](https://user-images.githubusercontent.com/63030569/122212685-1929cd00-cee3-11eb-821f-b6a69fec407c.png)

위 그림에서 빨간 선에 닿는 에지를 삭제하면 {a, b, c}로 구성된 그래프와 {d, e, f}로 구성된 그래프로 나뉜다.
이 때 빨간 선에 닿아 있는 에지의 집합을 Cut이라고 한다.

최소 신장 트리의 Cut property란, **Cut에서 최소 비용 에지를 포함하는 MST는 최소 하나 이상 존재한다**는 것을 말한다.

위 예시에서 Cut을 구성하는 에지 중 최소 비용인 에지는 비용이 6이다. 앞의 예시에서 살펴봤듯이 비용이 6인 에지는 위 그래프의
최소 신장 트리에 포함된다.

### Cut property 증명

Cut property는 귀류법으로 쉽게 증명할 수 있다.

아래와 같이 그래프 a, b가 cut으로 연결되어 있고 최소 에지 e와, 최소가 아닌 에지 e'이 있다.

![image](https://user-images.githubusercontent.com/63030569/122214224-cf41e680-cee4-11eb-8523-dad1823e1633.png)


**가정: e를 포함하는 MST가 존재하지 않는다.**

MST가 존재하려면 어쨋든 Cut에 속한 에지 중 하나는 반드시 포함해야하므로, 어떤 MST T는 e'을 포함한다.

트리의 기본 속성에 따라 T에 e'을 삭제하고 e를 추가하면 어떤 새로운 트리 T'이 생긴다.

`T' = T - {e'} + {e}`

이때, `cost(e) < cost(e')`이므로, `cost(T') < cost(T)`이다. 그런데 이는 e를 포함하는 MST가 존재하지 않는다는
가정에 모순되므로 Cut property는 참이다.

Cut property를 이용해 MST를 구성하는 알고리즘이 프림 알고리즘이다.

## Cycle property

**임의의 Cycle에 포함된 최대 비용 에지를 포함하는 MST는 존재하지 않는다.**

Cut property가 어떤 에지가 MST에 포함되어야 하는가에 대한 속성이라면, Cycle property는 어떤 에지가 MST에 포함되어서는 안되는가와 관련된 속성이다.

Cycle property도 귀류법으로 위와 비슷하게 증명할 수 있다.

Cycle property를 이용해 MST를 구성하는 알고리즘이 크루스칼 알고리즘이다.


