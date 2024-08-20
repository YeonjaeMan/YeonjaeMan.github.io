---
title: "최소비용으로 계단오르기 (Min Cost Climbing Stairs)"
author: yeon
date: 2024-08-21 01:26:00 +0900
categories: [DEV, Algorithm]
tags: [CodingTest]
---

## 문제
n개의 계단이 있으며, 각 계단마다 올라가는 데 드는 비용이 주어진다. 우리는 한 번에 1칸 또는 2칸을 오를 수 있으며, 최종 계단에 도착하기 위한 최소 비용을 구해야 한다.

## 예시
Example 1:   
Input. cost = [10, 15, 20]   
Output. 15   

Example 2:
Input. cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]   
Output. 6   

## 풀이 방법   
최종 계단부터 최소비용을 생각해보자.   

Example 1의 cost = [10, 15, 20]에서 15와 20의 입장에서 최종 계단에 도착하기 위한 최소 비용은 각각 15와 20이다. 다음으로 10의 입장에서 최종 계단에 도착하기 위한 최소 비용은 자기 자신인 10 + min(15, 20) = 25이다. 다음으로 출발 지점에서 최종 계단에 도착하기 위한 최소 비용은 0 + min(25, 15) = 15인 것이다. 즉, 두번째 계단부터 시작하면 최소 비용 15가 나오는 것을 알 수 있다.

Example 2도 마찬가지로 최종 계단부터 최소 비용을 계산해보면 6이 나온다.

## 코드 구현
`java
public class Test {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7};
        System.out.println(minCostClimbingStairs(arr)); // 12
    }

    private static int minCostClimbingStairs(int[] cost) {
        int case1 = 0, case2 = 0, current;
        for(int i = cost.length - 1; i >= 0; i--) {
            current = cost[i] + Math.min(case1, case2);
            case2 = case1;
            case1 = current;
        }
        return Math.min(case1, case2);
    }
}
`

## 시간 복잡도 및 공간 복잡도
계단의 개수를 n개라고 했을 때 위의 코드의 시간 복잡도는 배열의 마지막부터 0번째까지 반복하므로 O(n)이고, 공간복잡도는 다른 배열 선언 없이 current라는 변수를 이용해서 최소 비용을 계산하므로 O(1)이다.

## 참고
[최소비용으로 계단오르기](https://www.youtube.com/watch?v=EKHFu9vB-Oc&list=PLjSkJdbr_gFbOjb0mQomQJbxmG5Wbofxw)