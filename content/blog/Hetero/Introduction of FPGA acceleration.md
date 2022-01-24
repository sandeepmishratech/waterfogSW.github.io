---
title:  "[FPGA]Introduction of FPGA acceleration"
date: 2021-07-06 16:21:13
category: 'Hetero'
draft: false
---

## CPU vs FPGA
보통 CPU 벤치마킹은 실행 시간과 실행 속도를 명시하지만 FPGA 성능은 데이터 처리량을 기준으로 하기 때문에 CPU와 FPGA를 단순 비교하기는 어렵습니다. 예를 들어 CPU에는 각각 2.4GHz로 실행되는 4개의 코어가 존재하지만, FPGA는 62MHz로 상대적으로 속도 측면에서 CPU에 비해 상당히 느리게작동하는것처럼 보입니다. 하지만 FPGA는 병렬처리 특성을 이용하여 throughput을 크게 향상시킵니다.

![image](https://www.stemmer-imaging.com/media/cache/default_image_dialog/uploads/te/tech-tipp-fpga-speed-comparison.jpg)

먼저 FPGA가 얼마나 빨리 실행될 수 있는가에 대한 문제보다는 FPGA가 처리할 수 있는 데이터 전송 속도에 집중해야 합니다. 위의 이미지를 데이터 흐름의 예로 보면 속도는 방정식 의 일부 변수에 불과하다는 점을 알 수 있습니다. 데이터는 FPGA로 들어오고, 병렬 처리되도록 분할되며, 데이터는 다양한 기능(논리 블록을 사용하여 설정)으로 공급됩니다. 그런 다음 FPGA는 처리된 데이터를 출력합니다. 이러한 작업은 실시간으로 일어납니다.

1. 데이터는 카메라 출력 속도로 수신됩니다(62MHz).
2. 8개의 병렬 프로세스로 분할되므로 데이터 전송 속도는 496MOPS(Million operations per second)입니다.
3. 여러 처리 단계를 거칩니다. (2480 MOPS)
4. 병렬이 제거되고 62MHz의 일정한 속도로 메모리에 출력됩니다.