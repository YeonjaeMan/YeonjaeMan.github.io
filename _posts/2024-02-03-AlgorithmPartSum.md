---
title: "[알고리즘]2차원 배열의 구간 합 구하기"
author: yeon
date: 2024-02-03 00:34:00 +0800
categories: [DEV, Algorithm]
tags: [CodingTest]
---

### 문제

시간 제한 : 1초

N x N개의 수가 N x N 크기의 표에 채워져 있다.   

표 안의 수 중 (X1, Y1)에서 (X2, Y2)까지의 합을 구하려 한다.   

X는 행, Y는 열을 의미한다.   

예를 들어 N = 4이고, 표가 다음과 같이 채워져 있을 때를 살펴보자.   

(2,2)에서 (3,4)까지의 합을 구하면 3 + 4 + 4 + 5 + 5 + 6 = 27이고, (4,4)에서 (4,4)까지의 합을 구하면 7이다.   

표에 채워져 있는 수와 합을 구하는 연산이 주어졌을 때 이를 처리하는 프로그램을 작성하시오.


| 행\열 | 1열 | 2열 | 3열 | 4열 |
| --- | --- | --- | --- | --- |
| 1행 | 1 | 2 | 3 | 4 |
| 2행 | 2 | 3 | 4 | 5 |
| 3행 | 3 | 4 | 5 | 6 |
| 4행 | 4 | 5 | 6 | 7 |

### 입력

1번째 줄에 표의 크기 N과 합을 구해야 하는 횟수 M이 주어진다(1 <= N <= 1024, 1 <= M <= 100,000).   

2번째 줄부터 N개의 줄에는 표에 채워져 있는 수가 1행부터 차례대로 주어진다.   

다음 M개의 줄에는 4개의 정수 X1, Y1, X2, Y2가 주어지며, (X1, Y1)에서 (X2, Y2)의 합을 구해 출력해야 한다.   

표에 채워져 있는 수는 1,000보다 작거나 같은 자연수다(X1 <= X2, Y1 <= Y2).   

### 출력

총 M줄에 걸쳐 (X1, Y1)에서 (X2, Y2)까지 합을 구해 출력한다.   

| 예제 입력 1 | 예제 출력 1 |
| --- | --- |
| 4 3 |
| 1 2 3 4 |
| 2 3 4 5 |
| 3 4 5 6 |
| 4 5 6 7 |
| 2 2 3 4 | 27 |
| 3 4 3 4 | 6 |
| 1 1 4 4 | 64 |

| 예제 입력 2 | 예제 출력 2 |
| --- | --- |
| 2 4 |
| 1 2 |
| 3 4 |
| 1 1 1 1 | 1 |
| 1 2 1 2 | 2 |
| 2 1 2 1 | 3 |
| 2 2 2 2 | 4 |

### 문제 풀이

2차원 구간 합 배열 S를 만들어준다.   

(S[i][j] = S[i][j - 1] + S[i - 1][j] + S[i - 1][j - 1] + A[i][j])   

(X1, Y1)부터 (X2, Y2)까지의 합을 구한다.   

(S[x2][y2] - S[x2][y1 - 1] - S[x1 - 1][y2] + S[x1 - 1][y1 - 1])   


### 코드 구현하기

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.io.IOException;

public class Main {
    
    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());

        int N = Integer.parseInt(st.nextToken());
        int M = Integer.parseInt(st.nextToken());
        int[][] S = new int[N + 1][N + 1];

        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 1; j <= N; j++) {
                S[i][j] = S[i][j - 1] + S[i - 1][j] - S[i - 1][j - 1] + Integer.parseInt(st.nextToken());
            }
        }

        for(int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            int x1 = Integer.parseInt(st.nextToken());
            int y1 = Integer.parseInt(st.nextToken());
            int x2 = Integer.parseInt(st.nextToken());
            int y2 = Integer.parseInt(st.nextToken());
            int answer = 0;

            answer = S[x2][y2] - S[x1 - 1][y2] - S[x2][y1 - 1] + S[x1 - 1][y1 - 1];
            System.out.println(answer);            
        }
        
    }
}
```

### 정리

문제를 처음 읽었을 때 이해가 전혀 되질 않았다.   

(2,2)에서 (3,4)까지의 합을 구하는데 어떻게 결과가 27이 나오는 것인지 문제를 반복해서 읽어도 이해가 되지 않았다.   

글을 쓰면서 책을 보지 않고 다시 풀면서도 문제 이해를 한 번 더 해야 했다.   

나는 (2,2)에서 (3,4)까지라고 하면 (2,2) (2,3) (2,4) (3,2) (3,3) (3,4) 인 영역 안의 수를 더하는 것이라고 이해했다.   

문제를 이해하니 영역이 눈에 보였고, 어떻게 풀어야 할 것인지는 쉽게 보였다.   

2차원 구간 합 배열을 만들고, 구간 합 배열의 연산을 통해 구하고자하는 값을 구했다.   