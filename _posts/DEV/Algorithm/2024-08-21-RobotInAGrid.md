---
title: "Robot in a Grid"
author: yeon
date: 2024-08-21 12:59:00 +0900
categories: [DEV, Algorithm]
tags: [CodingTest]
---

## 문제
로봇이 격자의 왼쪽 상단 모서리에 앉아 있습니다.

로봇은 오직 두 방향(오른쪽과 아래쪽)으로만 이동할 수 있습니다.

특정 셀은 "출입 금지"로 지정되어 있어 로봇이 그 위에 발을 디딜 수 없습니다.

로봇이 왼쪽 상단에서 오른쪽 하단으로 가는 경로를 찾는 알고리즘을 설계하세요.

![문제 그림](/assets/img/RobotInAGrid/robotinagrid1.png)

## 생각해보기
내가 생각한 방법은 그리드에서 오른쪽, 아래로 갈 수 없는 길은 미리 X 표시를 해놓고 로봇을 오른쪽 > 아래 순으로 이동하면서 경로를 출력하려고 생각했다.

이 방법은 2차원 배열인 그리드를 돌면서 오른쪽, 아래로 이동할 수 있는지 없는지 검사하는 데 O(²)의 시간 복잡도가 걸려 비효율적일 것 같다.

## 풀이 방법
경로를 출력해야 하기 때문에 Goal 지점에서 출발해서 경로를 저장하면서 로봇이 서있는 위치까지 갔다가 경로를 출력하면서 Goal 지점까지 이동한다. 즉, **재귀 호출**을 이용한다.

경로를 저장할 때 왼쪽 > 위쪽 순으로 이동하고, 이동할 수 없는 범위 밖이거나 막혀있는 경우는 재귀 호출을 빠져나온다.

![왼쪽 범위 밖](/assets/img/RobotInAGrid/robotinagrid2.png)

![위쪽 막힘](/assets/img/RobotInAGrid/robotinagrid4.png)

![재귀 호출 빠져나오기](/assets/img/RobotInAGrid/robotinagrid5.png)

![위쪽 막힘](/assets/img/RobotInAGrid/robotinagrid6.png)

이런 식으로 로봇의 위치를 찾아간다.

![로봇의 위치](/assets/img/RobotInAGrid/robotinagrid7.png)

로봇의 위치를 찾았다면 경로가 저장되어 있기 때문에 재귀 호출을 빠져나오면서 경로를 출력해주면 된다.

![결과](/assets/img/RobotInAGrid/robotinagrid3.png)

## 코드 구현
- 점의 위치를 저장할 Point class
```java
public class Point {
    public int row, col;

    public Point(int row, int col) {
        this.row = row;
        this.col = col;
    }
}
```

- 알고리즘을 작성할 Solution class
```java
public class Solution {
    public ArrayList<Point> findPath(boolean[][] grid) {
        // null 체크
        if(grid == null || grid.length == 0)
            return null;
        // 경로를 저장할 객체 생성
        ArrayList<Point> path = new ArrayList<>();
        // 재귀 메소드에 Goal 지점의 위치를 함께 넘겨준다.
        if(findPath(grid, grid.length - 1, grid[0].length - 1, path)) {
            return path;
        } else {
            return null;
        }
    }

    private boolean findPath(boolean[][] grid, int row, int col, ArrayList<Point> path) {
        // grid가 범위 밖이거나 막혀있을 때 false 반환
        if(isInRange(grid, row, col) || grid[row][col]) {
            return false;
        }
        // 로봇이 서있는 위치인지 먼저 확인 > 왼쪽 이동 > 위쪽 이동
        if((row == 0 && col == 0) || findPath(grid, row, col - 1, path) || findPath(grid, row - 1, col, path)) {
            Point p = new Point(row, col);
            // 경로 저장
            path.add(p);
            return true;
        }
        return false;
    }

    private boolean isInRange(boolean[][] grid, int row, int col) {
        return row >= 0 && row <= grid.length - 1 && col >= 0 && col <= grid[row].length - 1;
    }
}
```

- 결과를 확인할 Test class
```java
public class Test {
    private static void main(String[] args) {
        boolean[][] grid = {
                {false, false, false, false},
                {false, false, false, true},
                {true, true, false, false},
                {false, false, false, false}
        };
        Solution sol = new Solution();
        ArrayList<Point> path = sol.findPath(grid);
        if(path != null)
            for(Point p : path)
                System.out.print(p.row + "" + p.col + " -> ");
    }
}
```

- 출력 결과

![출력 결과](/assets/img/RobotInAGrid/robotinagrid8.png)

## 알게된 것
- 2차원 배열 grid를 boolean 타입으로 선언하여 막힌 길을 표시할 수 있다.
- 컴퓨터에게는 규칙이 필요하다. 어느 점이든 왼쪽을 먼저 확인하고 위쪽을 확인한다.
- 출발지점과 목표지점이 있다면 목표지점에서 출발지점으로 가는 경로를 생각해보자.

## 참고

[Robot in a Grid](https://www.youtube.com/watch?v=WdqJqZqEJuw&list=PLjSkJdbr_gFbOjb0mQomQJbxmG5Wbofxw&index=2)