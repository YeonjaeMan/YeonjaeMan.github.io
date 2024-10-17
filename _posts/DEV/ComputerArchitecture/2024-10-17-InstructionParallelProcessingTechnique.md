---
title: "명령어 병렬 처리 기법"
author: yeon
date: 2024-10-17 23:26:00 +0900
categories: [DEV, ComputerArchitecture]
tags: [파이프라인, 슈퍼스칼라, 비순차적 명령어 처리]
---

![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image.png)

## 명령어 파이프라인

- 명령어가 처리되는 과정을 비슷한 시간 간격으로 나누면?
    1. 명령어 인출(Instruction Fetch)
    2. 명령어 해석(Instruction Decode)
    3. 명령어 실행(Execute Instruction)
    4. 결과 저장(Write Back)
- 같은 단계가 겹치지만 않는다면 CPU는 '각 단계를 동시에 실행할 수 있다'
![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image-1.png)
- 즉, 명령어 파이프라이닝은 동시에 여러 개의 명령어를 겹쳐 실행하는 기법이다.
> Q: 명령어 파이프라인을 사용하지 않았다면?   
A: 아래 그림과 같이 시간이 더 걸린다.   
![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image-2.png)
- 파이프라인 위험: 명령어 파이프라인이 성능 향상에 실패하는 경우
    - 데이터 위험: 명령어 간의 의존성에 의해 야기
        - 모든 명령어를 동시에 처리할 수는 없다.(이전 명령어를 끝까지 실행해야만 비로소 실행할 수 있는 경우)
        ![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image-3.png)
    - 제어 위험: 프로그램 카운터의 갑작스러운 변화
    ![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image-4.png)
    > 분기 예측(branch prediction): 제어 위험을 방지하기 위해 프로그램 카운터가 어디로 이동할 지 미리 예측하는 기술
    - 구조적 위험: 서로 다른 명령어가 같은 CPU 부품(ALU, 레지스터)를 사용하려고 할 때

## 슈퍼스칼라

![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image-5.png)

- CPU 내부에 여러 개의 명령어 파이프라인을 포함한 구조(오늘날의 멀티프로세서 스레드)
- 이론적으로는 파이프라인 개수에 비례하여 처리 속도 증가
- But, 파이프라인 위험도의 증가로 인해 파이프라인 개수에 비례하여 처리 속도가 증가하진 않음

## 비순차적 명령어 처리

![alt text](/assets/img/ComputerArchitecture/InstructionParallelProcessingTechnique/image-6.png)

- 합법적인 새치기
- 파이프라인의 중단을 방지하기 위해 명령어를 순차적으로 처리하지 않는 병렬 처리 기법
- 아무 명령어의 순서를 바꿀 수는 없음(전체 프로그램 실행 흐름에 영향이 없어야 함)