---
title: "명령어 사이클과 인터럽트"
author: yeon
date: 2024-10-14 23:51:00 +0900
categories: [DEV, ComputerArchitecture]
tags: [명령어 사이클, 인터럽트, 요청 신호, 플래그, 서비스 루틴, 벡터]
---

![alt text](/assets/img/ComputerArchitecture/Interrupt/image.png)

## 명령어 사이클
- 프로그램 속 명령어들은 일정한 주기가 반복되며 실행한다. 이 주기를 명령어 사이클이라고 한다.
- 인출 사이클: 가장 먼저 CPU로 갖고 온다.
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-1.png)
- 실행 사이클: 갖고 왔으면 실행한다.
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-2.png)
- 인출-실행-인출-실행-... 반복
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-3.png)

> 그런데 CPU로 명령어를 가지고 와도 바로 실행이 불가능한 경우도 있다.   
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-4.png)

- 위와 같은 간접 주소 지정 방식과 같이 메모리 접근이 더 필요한 경우는 간접 사이클이 추가된다.
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-5.png)

## 인터럽트
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-6.png)
- 인터럽트(interrupt): 방해하다, 중단시키다
    - 'CPU가 꼭 주목해야 할 때', 'CPU가 얼른 처리해야 할 다른 작업이 생겼을 때' 발생
    - ex. "강대리, 이거 급한거니까 지금 하던 일 멈추고 이것부터 처리해주게"

### 인터럽트 종류
- 동기 인터럽트(예외): CPU가 예기치 못한 상황을 접했을 때 발생
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-7.png)
- 비동기 인터럽트(하드웨어 인터럽트): 주로 입출력장치에 의해 발생
![alt text](/assets/img/ComputerArchitecture/Interrupt/image-8.png)

### 하드웨어 인터럽트
- 알림과 같은 인터럽트
- 입출력 작업 도중에도 효율적으로 명령어를 처리하기 위해 하드웨어 인터럽트 사용
> 입출력장치는 CPU에 비해 느리다.   
인터럽트가 없다면 CPU는 프린트 완료 여부를 확인하기 위해 주기적으로 확인해야 한다.   
인터럽트가 있다면 입출력 작업 동안 CPU는 다른 일을 할 수 있다.

### 하드웨어 인터럽트의 처리 순서
1. 입출력장치는 CPU에 **인터럽트 요청 신호**를 보낸다.
    - 인터럽트 요청 신호: CPU의 작업을 방해하는 인터럽트에 대한 요청
2. CPU는 실행 사이클이 끝나고 명령어를 인출하기 전 항상 인터럽트 여부를 확인한다.
3. CPU는 인터럽트 요청을 확인하고 **인터럽트 플래그**를 통해 현재 인터럽트를 받아들일 수 있는지 여부를 확인한다.
    - 인터럽트 플래그: 인터럽트 요청 신호를 받아들일지 무시할지를 결정하는 비트
4. 인터럽트를 받아들일 수 있다면 CPU는 지금까지의 작업을 백업한다.
5. CPU는 **인터럽트 벡터**를 참조하여 **인터럽트 서비스 루틴**을 실행한다.
    - 인터럽트 벡터: 인터럽트 서비스 루틴의 시작 주소를 포함하는 인터럽트 서비스 루틴의 식별 정보
    - 인터럽트 서비스 루틴: 인터럽트를 처리하는 프로그램
6. 인터럽트 서비스 루틴 실행이 끝나면 4에서 백업해 둔 작업을 복구하여 실행을 재개한다.
- 인터럽트의 종류를 막론하고 인터럽트 처리 순서는 대동소이하다.
- 하드웨어 인터럽트는 막을 수 있는 인터럽트, 막을 수 없는 인터럽트로 구분된다.