---
layout: post
title:  "백준 1005번 문제(Python)"
subtitle:   "미완"
categories: Algorithm
tags: 백준문제
order: 1
---

### 실패한 아이디어
구하고자 하는 빌딩으로부터 BFS 를 활용하여 MAX 값을 구하자 !
결과는 정상적으로 출력되지만 이는 시간 초과 문제가 발생했다.

* 구현 코드

~~~
import sys
import heapq


# goal 에서 시작 ( start = goal )
def dijkstra(graph, start, time_list):
    # 최대값을 구할 것이므로 0에서 시작
    times = {node: 0 for node in graph}
    # 시작 건물을 짓는 데 걸리는 시간
    times[start] = time_list[start-1]
    queue = []

    # 큐에 push
    heapq.heappush(queue, [times[start], start])

    while queue:
        # 탐색 하고자 하는 건물
        current_time, current_building = heapq.heappop(queue)

        # 걸리는 시간이 더 짧다면 통과
        if times[current_building] > current_time:
            continue

        # 해당 지점의 번호와 시간을 통해 계산
        for new_building, new_time in graph[current_building].items():
            time = new_time + current_time
            if time > times[new_building]:
                times[new_building] = time
                heapq.heappush(queue, [time, new_building])

    return times


def problem1005():
    cycle = int(sys.stdin.readline().strip())
    for i in range(1, cycle+1):
        graph = {}
        building_number, rule_number = map(int, sys.stdin.readline().strip().split())
        for num in range(1, building_number+1):
            graph[num] = {}
        time_list = list(map(int, sys.stdin.readline().strip().split()))
        for rule in range(rule_number):
            start, end = map(int, sys.stdin.readline().strip().split())
            graph[end][start] = time_list[start-1]
        goal = int(sys.stdin.readline())
        result = dijkstra(graph, goal, time_list)
        sys.stdout.write(str(max(result.values())))
~~~

### 성공 사례 아이디어
**위상 정렬**과 **DP** 를 사용해야 된다고 한다 !

#### 1. 위상 정렬(Topological Sort)이란 ?
    순서가 있는 작업을 차례로 수행해야 하는 경우, 순서를 결정해주는 알고리즘
    = 방향 그래프에 존재하는 각 노드들의 선행 순서를 위반하지 않으면서 모든 노드를 나열하는 알고리즘
    

#### 2. 위상 정렬의 특징
* 위상 정렬의 결과는 여러가지가 나올 수 있다 !
* 위상 정렬 과정 중 그래프에 남아 있는 노드 중 진입 차수가 0인 노드가 없다면, 싸이클이나 간선이 없는 노드가 있다는 것 !
* 즉, 2가지를 알 수 있다.
**현재 그래프가 위상 정렬이 가능한지(=싸이클이 존재하는지)**
**위상 정렬의 결과**

#### 3. 과정
일반적으로 큐를 사용하는 기법이 많은 것 같다.
큐를 사용한 과정은 다음과 같다

1. 진입차수가 0인 노드를 큐에 삽입한다.
2. 그래프에서 큐에 삽입된 노드의 모든 간선을 제거한다.
3. 간선 제거 이후에 진입차수가 0이 된 노드를 큐에 삽입한다.
4. 큐에 빌 때까지 2~3 과정을 반복한다.

이 때, 모든 노드를 탐색하지 않았는데 큐가 비어있다면 사이클이 존재하는 것이다.
모든 노드를 탐색하였다면, 큐에서 꺼낸 순서가 위상 정렬의 결과가 된다.

#### 4. 1005번 해결
위상 정렬과 DP(Dynamic Programming) 을 활용한 코드는 다음과 같다.

~~~
import sys
from collections import deque


def problem1005():
    cycle = int(sys.stdin.readline().strip())
    for i in range(1, cycle+1):
        building_number, rule_number = map(int, sys.stdin.readline().strip().split())
        time_list = list(map(int, sys.stdin.readline().strip().split()))
        # 건설 순서 규칙
        rule_list = [[] for _ in range(building_number)]
        # 진입 차수
        in_degree_list = [0 for _ in range(building_number)]
        # 해당 건물까지 걸리는 시간
        time_DP = [0 for _ in range(building_number)]

        # 리스트에 규칙을 저장
        for rule in range(rule_number):
            start, end = map(int, sys.stdin.readline().strip().split())
            rule_list[start-1].append(end-1)
            in_degree_list[end-1] += 1

        queue = deque()
        # 진입차수가 0 인 빌딩을 찾아 queue 에 삽입
        for index in range(building_number):
            if in_degree_list[index] == 0:
                queue.append(index)
                time_DP[index] = time_list[index]

        while queue:
            building_index = queue.popleft()
            for destination in rule_list[building_index]:
                in_degree_list[destination] -= 1
                time_DP[destination] = max(time_DP[building_index] + time_list[destination], time_DP[destination])
                if in_degree_list[destination] == 0:
                    queue.append(destination)

        goal = int(sys.stdin.readline())
        print(time_DP[goal-1])
~~~


#### 느낀점

백준 골드 난이도 문제는 처음 해결해보는데, 굉장히 어려웠다.
그래프를 활용한다에서 생각이 멈추었었는데.. 다양한 문제를 풀어봐서 깨달아야 겠다 !!