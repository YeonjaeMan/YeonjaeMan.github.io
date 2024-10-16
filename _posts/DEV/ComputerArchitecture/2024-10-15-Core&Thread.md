---
title: "빠른 CPU를 위한 설계 기법"
author: yeon
date: 2024-10-16 23:54:00 +0900
categories: [DEV, ComputerArchitecture]
tags: [CPU, 코어, 스레드]
---

## CPU의 속도를 빠르게 만들려면?

- 컴퓨터 부품들은 ‘클럭 신호’에 맞춰 일사분란하게 움직인다.
- CPU는 ‘명령어 사이클’이라는 정해진 흐름에 맞춰 명령어들을 실행한다.

## 클럭 속도

- 클럭 속도: 헤르츠(Hz) 단위로 측정
- 헤르츠(Hz): 1초에 클럭이 반복되는 횟수
- 클럭이 ‘똑-딱-’ 하고 1초에 한 번 반복되면 1Hz
- 클럭이 1초에 100번 반복되면 100Hz

> Q: 클럭 신호를 마냥 높이면 CPU가 무지막지하게 빨라지느냐?   
A: No, 필요 이상으로 클럭을 높이면 발열이 심각해짐   
Q: 클럭 속도를 늘리는 방법 외?   
A: 코어 수를 늘린다, 스레드 수를 늘린다.

## 코어 & 멀티코어

![alt text](/assets/img/ComputerArchitecture/Core&Thread/image.png)

- 코어(core)란?
    - 현대적인 과점에서 “CPU”라는 용어를 재해석 해야함
    - ‘명령어를 실행하는 부품’
    - 전통적으로 ‘명령어를 실행하는 부품’은 원칙적으로 하나만 존재
    - But, 오늘날 CPU에는 ‘명령어를 실행하는 부품’이 여러개 존재
    - ‘명령어를 실행하는 부품’을 코어라는 용어로 사용
- 멀티코어 프로세서: 여러 개의 코어를 포함하고 있는 CPU
![alt text](/assets/img/ComputerArchitecture/Core&Thread/image-1.png)

> Q: 코어를 두 개, 세 개, .. 100개 늘리면 연산 속도도 그에 비례해서 빨라지는가?   
A: No, 꼭 코어 수에 비례하여 증가하지는 않는다.

## 스레드 & 멀티스레드

- 스레드(thread)란?
![alt text](/assets/img/ComputerArchitecture/Core&Thread/image-3.png)
    - ‘실행 흐름의 단위’
    - 하드웨어적 스레드(’논리 프로세서’라고도 함): 하나의 코어가 동시에 처리하는 명령어 단위
        - 멀티스레드 프로세서를 실제로 설계하는 일은 매우 복잡하지만, 가장 큰 핵심은 레지스터
        ![alt text](/assets/img/ComputerArchitecture/Core&Thread/image-5.png)
    - 소프트웨어적 스레드: 하나의 프로그램에서 독립적으로 실행되는 단위
    ![alt text](/assets/img/ComputerArchitecture/Core&Thread/image-2.png)
        - 사용자로부터 입력받은 내용을 화면에 보여주는 기능
        - 사용자가 입력한 내용이 맞춤법에 맞는지 검사하는 기능
        - 사용자가 입력한 내용을 수시로 저장하는 기능
        ![alt text](/assets/img/ComputerArchitecture/Core&Thread/image-4.png)

> 1코어 1스레드 CPU도 여러 소프트웨어적 스레드를 만들 수 있다!