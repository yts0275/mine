---
layout: post
title:  "백준 1005번 문제"
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
* 순서가 있는 작업을 차례로 수행해야 하는 경우, 순서를 결정해주는 알고리즘

#### 2. 위상 정렬의 특징

#### 3. 과정
