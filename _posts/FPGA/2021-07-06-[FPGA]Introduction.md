---
layout: article
title:  "[FPGA]Introduction of FPGA"
categories:
  - FPGA
tags:
  - FPGA
cover: https://user-images.githubusercontent.com/28651727/124542831-1a557680-de5f-11eb-94e4-a6161b632358.png
---

<div align=center>
<img src="https://user-images.githubusercontent.com/28651727/124542831-1a557680-de5f-11eb-94e4-a6161b632358.png"/>
</div>

## What is FPGA?
- Field Programmable은 장치를 언제 어디서나 프로그래밍 할 수 있음을 뜻합니다.
- Gate Array는 논리 게이트의 규칙적인 배열을 의미합니다. FPGA에서는 논리게이트들의 interconnect로 기능을 구현할 수 있습니다. 

## FPGA vs ASIC

<div align=center>
<img src="https://user-images.githubusercontent.com/28651727/124542741-ea0dd800-de5e-11eb-8674-09679647be68.png"/>
</div>

- Reporgrammable
  - design 또는 prototype을 만들고 검증할때 계속 업그레이드가 가능합니다.
- Multiple bitstreams
  - 앞선 Reprogrammable의 연장선으로 ASIC은 개발이 완료되어 출시되면 하나의 기능만 가능하지만, 다양한 bit streams을 올릴 수 있음.
- Cost of Bug fix
  - HW에서의 bug fix가 가능합니다.

무엇보다도 FPGA는 수많은 logic block을 서로 연결하여 대규모 병렬, 실시간 처리를 이루어낼 수 있다는 점에서 큰 장점을 가집니다.

> ### What is ASIC? 
> 응용 프로그램별 집적 회로 또는 ASIC는 다양한 시장의 특정 응용 분야를 위해 설계 및 구현되는 IC입니다. ASIC는 기업이 BOM(Bill of Material) 비용을 절감하고 성능 요구사항을 개선하고자 할 때 매우 비용 효율적입니다. 일반적으로 ASIC를 reversing하는 것은 매우 어렵습니다. 장치를 개발하는 데 사용되는 독점적인 전자 부품 때문입니다. 회사에서 자체 설계에 개별 또는 표준 IC를 사용하는 경우 경쟁업체가 유사 제품을 개발하는 것이 더 쉽습니다. ASIC는 전체 제품 수명 동안 일관된 논리 기능을 가져야 하는 제품에 가장 적합합니다.

## Architecture
### Introductino to FPGA Resource 
![img](https://media.springernature.com/lw685/springer-static/image/art%3A10.1007%2Fs40031-020-00508-y/MediaObjects/40031_2020_508_Fig3_HTML.png)

FPGA는 logic block, I/O cells, interconnection resources로 구성되어 있습니다. 
- CLB(Configurable Logic Block)
  - CLB는 FPGA의 기본적인 logic 리소스로, routing으로 통해 연결되어 복잡한 logic function을 수행할 수 있습니다.
  - **Flip-Flop** : FPGA에서의 가장 작은 storage resource로 각 CLB의 flip-flop은 clock cycle사이에서의 논리적인 상태를 저장하는데 사용되는 binary register입니다.
  - **Look-up Table(LUT)** : 모든 combination function을 수정할 수 있으며, 입력과 출력의 정의를 table로 작성합니다. 즉, 입력값 조합에 대해 원하는 출력 값을 포함하는 작은 메모리라 볼 수 있습니다.
  - **Multiplexer** : 두 개 이상의 입력 중에서 선택한 입력을 반환하는 회로입니다.
- I/O block FPGA 외부의 소자와 통신을 위해 IOB 사용
- Routing(Promgrammable Interconnection)
  - CLB, IOB와 같은 FPGA내 function component의 입력과 출력 사이의 신호 path를 프로그래밍 할 수 있는 네트워크입니다.
  - Vertical Routing Channel
  - Horizontal Routing Channel
