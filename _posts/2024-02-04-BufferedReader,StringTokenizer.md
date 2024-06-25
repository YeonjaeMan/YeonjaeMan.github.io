---
title: "[JAVA]BufferedReader, StringTokenizer"
author: yeon
date: 2024-02-04 00:34:00 +0800
categories: [DEV, Java]
tags: [BufferedReader, StringTokenizer]
---

# 사용 예제

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    
    public static void main(String[] args) throws IOException {
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());

        int N = Integer.parseInt(st.nextToken());
        int M = Integer.parseInt(st.nextToken());
    }
}
```

# 예제 설명

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
```

위의 코드를 풀어서 써보자.   

```java
InputStream inputStream = System.in;
InputStreamReader sr = new InputStreamReader(inputStream);
BufferedReader br = new BufferedReader(sr);
```

InputStream은 입력받은 데이터는 int형으로 저장하고, 1 byte만 읽는다는 것이다.   

InputStreamReader는 InputStream을 통해 받아온 바이트 단위 데이터를 문자 단위 데이터로 변환해주고, char 배열로 데이터를 받을 수 있게 한다.   

BufferedReader는 char 배열 형태로 받아온 데이터를 버퍼(buffer)라는 저장공간에 char형으로 쌓아 저장한다.   

```java
StringTokenizer st = new StringTokenizer(br.readLine());
```

br.readLine()은 BufferedReader에 쌓인 char형 데이터를 전부 꺼내 문자열로 보내준다.   

주의점은 br.readLine()을 사용하기 위해서는 IOException에 대한 예외 처리가 필요하다.   

StringTokenizer는 띄어쓰기는 제외하고 문자열을 토큰화시켜준다.   

```java
int N = Integer.parseInt(st.nextToken());
int M = Integer.parseInt(st.nextToken());
```

st.nextToken()은 토큰화시킨 문자열에서 한 토큰씩 가져온다.   

예를 들어, 입력을 3 5 를 하고 엔터를 친다면 N은 3, M은 5의 값을 가지게 된다.   

# Scanner와 BufferedReader

최근 알고리즘 공부를 하는데 <Do it! 알고리즘 코딩 테스트(자바편)> 책을 이용하고 있다.   

책에서 주로 백준 사이트에 있는 코딩테스트 문제를 다룬다.   

자바를 배울 때 Scanner로 주로 사용자의 입력을 받았었다.   

Scanner와 BufferedReader의 큰 차이점으로는 Scanner보다 BufferedReader의 입력받는 속도가 훨씬 빠르다는 점이다.   

코딩테스트의 특성상 풀이의 시간 복잡도를 필요에 의해 줄여야 할 필요가 있다.   

주로 사용하는 언어가 JAVA라면 코딩테스트 문제의 입력을 받는 부분에서 BufferedReader와 StringTokenizer가 자주 쓰여 알고 있는 것이 좋겠다.   