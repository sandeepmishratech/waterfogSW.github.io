---
title: "[Env]WSL2 커널이미지 컴파일"
excerpt: WSL2 커널이미지 컴파일
categories:
  - 'Env'
tags:
  - 'Kernel'
  - 'WSL'
last_modified_at: 2021-04-01T08:06:00-05:00
toc : true
---

기존 WSL2은 모든 드라이버가 컴파일 되어있는 커스텀 커널을 사용합니다. 커널 모듈을 지원하지만 시스템에는 모듈이 포함되어 있지 않습니다. WSL2의 `/lib/modules`디렉토리를 확인해 보면 텅 비어있는것을 확인하실수 있습니다. 

>WSL1은 windows NT Kerenl을 사용하기 때문에 리눅스 커널을 지원하지 않습니다.  
>배포 버전을 WSL1에서 WSL2로 변경하는것은 다음 문서를 참조하시길 바랍니다.(https://docs.microsoft.com/ko-kr/windows/wsl/install-win10)

WSL2-Ubuntu 18.04LTS~20.04LTS 에서 진행하였습니다.

```sh
sudo apt update
sudo apt install build-essential libncurses5-dev libssl-dev bison flex libelf-dev
git clone https://github.com/microsoft/WSL2-Linux-Kernel.git
cd WSL2-Linux-Kernel
make KCONFIG_CONFIG=Microsoft/config-wsl -j<코어개수>
```

make 도중 다음과 같은 선택지가 나오게 되는데 TSX는 동시성 프로그래밍을 위한 하드웨어 지원기술입니다. 현재는 Meltdown공격으로 인한 보안이슈가 발생하여 지원이 중지되었다고 합니다. 저는 OFF를 

```
TSX enable mode
> 1. off (X86_INTEL_TSX_MODE_OFF) (NEW)
  2. on (X86_INTEL_TSX_MODE_ON) (NEW)
  3. auto (X86_INTEL_TSX_MODE_AUTO) (NEW)
choice[1-3?]: 
```

make가 완료되면 WSL2-Linux-Kernel폴더에 커널 이미지 파일(`vmlinux`)가 생성되어있는것을 확인할 수 있습니다. (완료까지 약 10분가량 걸렸습니다.)
```
~/workspace/WSL2-Linux-Kernel$ ls
COPYING        LICENSES        README                 built-in.a  fs       lib              samples   usr
CREDITS        MAINTAINERS     README-Microsoft.WSL2  certs       include  mm               scripts   virt
Documentation  Makefile        System.map             crypto      init     modules.builtin  security  vmlinux
Kbuild         Microsoft       arch                   drivers     ipc      modules.order    sound     vmlinux.o
Kconfig        Module.symvers  block                  firmware    kernel   net              tools
```

이 이미지 파일을 윈도우 사용자 폴더에 복사합니다(윈도우 사용자 이름 확인하셔야 합니다)
```
cp vmlinux /mnt/c/Users/<사용자이름>
```

그리고 해당 사용자 폴더에 .wslconfig파일을 생성하고
```
sudo vi /mnt/c/Users/<사용자이름>/.wslconfig
````
vi편집기가 켜지면 다음과 같은 내용을 작성합니다.
```
[wsl2]
kernel=C:\\Users\\<사용자이름>\\vmlinux
```

이후 윈도우 파워쉘에서 wsl을 껐다가 켜줍니다.
```
wsl --shutdown
wsl
```

그런다음 uname -r 명령어를 입력하면 커널이 바뀐것을 확인하실수 있습니다.
```
$ uname -r
4.19.84-microsoft-standard+
```
