---
title: "[알고리즘]투 포인터"
author: yeon
date: 2024-02-04 00:34:00 +0800
categories: [DEV, Algorithm]
tags: [CodingTest]
---

### 문제

시간 제한 : 2초   

어떠한 자연수 N은 몇 개의 연속된 자연수의 합으로 나타낼 수 있다.   

당신은 어떤 자연수 N(1 <= N <= 10,000,000)을 몇 개의 연속된 자연수의 합으로 나타내는 가짓수를 알고 싶다.   

이때 사용하는 자연수는 N이어야 한다.   

예를 들어 15를 나타내는 방법은 15, 7 + 8, 4 + 5 + 6, 1 + 2 + 3 + 4 + 5이다.   

반면, 10을 나타내는 방법은 10, 1 + 2 + 3 + 4이다.   

N을 입력받아 연속된 자연수의 합으로 나타내는 가짓수를 출력하는 프로그램을 작성하시오.   

### 입력

1번째 줄에 정수 N이 주어진다.

### 출력

| 예제 입력 1 | 예제 출력 1 |
| --- | --- |
| 15 | 4 |

### 문제 풀이

구간 합 배열 S를 생성해 S[j] - S[i - 1]이 N이 나올 때마다 개수를 하나씩 세어주는 방법을 생각한다면 시간 제한이 2초이기 때문에 O(n^2)의 방법은 옳지 않다.   

또한, O(nlogn)의 시간 복잡도를 생각한다면 10,000,000 * log(10,000,000) = 약 2억3천만 이기에 시간 제한 2초를 넘는다.   

O(n^2), O(nlogn)보다 작은 시간 복잡도로 풀어야 한다는 점을 고려했을 때, O(n)의 투 포인터를 이용하는 방법이 있다.   

투 포인터는 startIndex와 endIndex 두 개의 변수를 이용하는 방법이다.   

startIndex부터 endIndex까지 합 sum을 구한다.   

만일 sum < N 이라면, sum값이 N의 값에 가까워지려면 sum이 증가해야하므로 endIndex의 위치를 하나 올린다.   

만일 sum > N 이라면, sum값이 N의 값에 가까워지려면 sum이 감소해야하므로 startIndex의 위치를 하나 올린다.   

만일 sum = N 이라면, 답의 개수를 하나 세주도록 한다.   

### 코드 구현

```java
import java.util.Scanner;

public class Main {
    
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int N = sc.nextInt();
        int startIndex = 1;
        int endIndex = 1;
        int sum = 1;
        int answer = 1;

        while(endIndex != N) {
            
            if(sum > N) {
                sum -= startIndex;
                startIndex++;
            } else if(sum < N) {
                endIndex++;
                sum += endIndex;
            } else {
                answer++;
                endIndex++;
                sum += endIndex;
            }
        }

        System.out.println(answer);
    }
}
```

### 정리

startIndex = 1, endIndex = 1로 초기화해 준 이유는 따로 1 ~ N 까지의 수를 저장하는 배열을 만들 필요없이 문제를 해결 할 수 있기 때문이다.   

sum = 1로 초기화해 준 이유는 startIndex와 endIndex의 처음 위치가 둘 다 1이기 때문이다.   

answer = 1로 초기화해 준 이유는 어떤 경우라도 N이라는 수를 분할할 때 N이라는 수 한 개도 경우의 수에 포함되기 때문이다.   

ex) 15 = 15, 7 = 7, ...   

투 포인터를 이용해서 연속된 자연수의 합을 구한다는 점이 흥미로웠다.   

나는 처음에 구간 합 배열을 이용해 풀려고 했었지만 시간 제한이라는 조건에 걸려 문제에 맞지 않는다는 것을 알았다.   

아직 시간 복잡도를 고려해야 하는 부분에서 감을 잡지 못하고 있다보니 다른 풀이를 떠올리기가 쉽지 않았다.   

앞으로도 알고리즘 공부를 꾸준히해서 고수가 되자..!   