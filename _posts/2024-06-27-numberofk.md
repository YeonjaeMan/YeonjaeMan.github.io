---
title: "K번째수"
author: yeon
date: 2024-06-27 22:22:00 +0800
categories: [DEV, Algorithm]
tags: [sort]
---

프로그래머스 알고리즘 고득점 Kit - 정렬에 해당하는 문제이다.

```java
import java.util.*;

class Solution {
    public int[] solution(int[] array, int[][] commands) {
        int[] answer = new int[commands.length];
        int ansIdx = 0;
        for(int[] command : commands) {
            int[] tmp = new int[command[1] - command[0] + 1];
            int idx = 0;
            for(int i = command[0] - 1; i < command[1]; i++) {
                tmp[idx] = array[i];
                idx++;
            }
            Arrays.sort(tmp);
            answer[ansIdx] = tmp[command[2] - 1];
            ansIdx++;
        }
        return answer;
    }
}
```

Level 1의 문제인만큼 어렵지 않게 풀었다.
하지만, [Arrays.sort()](https://yeonjaeman.github.io/posts/sort/) 메소드에 대해 제대로 알고 쓰지 않아서 정리해보았다.