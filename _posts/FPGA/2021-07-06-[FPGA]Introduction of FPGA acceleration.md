---
layout: article
title:  "[FPGA]Introduction of FPGA acceleration"
categories:
  - FPGA
tags:
  - FPGA
  - Hardware Acceleration
last_modified_at: 2021-07-06T08:06:00-05:00
cover: https://www.stemmer-imaging.com/media/cache/default_image_dialog/uploads/te/tech-tipp-fpga-speed-comparison.jpg
---

## CPU vs FPGA
CPU와 FPGA를 비교하기는 어렵습니다. CPU 벤치마킹은 대개 기능 실행 시간과 실행 속도를 명시하지만 FPGA 성능은 데이터 처리량을 기준으로 합니다. 최신 CPU는 GHz 순서로 실행됩니다. 예를 들어 최신 CPU에는 각각 2.4GHz로 실행되는 4개의 코어가 있을 수 있습니다. 상대적으로 속도 측면에서 FPGA는 62MHz로 매우 느리게 작동합니다. 이러한 차이는 상당히 크게 보이고 어떻게 FPGA가 빠르게 동작할 수 있는지에 대한 의문이 생깁니다. 이 부분에서 FPGA의 병렬 처리 특성이 나타납니다.

우리는 FPGA가 얼마나 빨리 실행될 수 있는가에 대한 문제보다는 FPGA가 처리할 수 있는 데이터 전송 속도에 집중해야 합니다. 위의 이미지를 데이터 흐름의 예로 보면 속도는 방정식 의 일부 변수에 불과하다는 점을 알 수 있습니다. 데이터는 FPGA로 들어오고, 병렬 처리되도록 분할되며, 데이터는 다양한 기능(논리 블록을 사용하여 설정)으로 공급됩니다. 예를 들어 이러한 기능은 6가지입니다. 그런 다음 FPGA는 처리된 데이터를 출력합니다. 이러한 모든 작업이 실시간으로 일어납니다

![image](https://www.stemmer-imaging.com/media/cache/default_image_dialog/uploads/te/tech-tipp-fpga-speed-comparison.jpg)

1. 데이터는 카메라 출력 속도로 수신됩니다(62MHz).
2. 8개의 병렬 프로세스로 분할되므로 데이터 전송 속도는 496MOPS(Million operations per second)입니다.
3. 여러 처리 단계를 거칩니다. (2480 MOPS)
4. 병렬이 제거되고 62MHz의 일정한 속도로 메모리에 출력됩니다.