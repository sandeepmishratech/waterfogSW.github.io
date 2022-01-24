---
title:  "[OS] Linux Kernel Structure"
date: 2021-11-17 16:21:13
category: 'ComputerScience'
draft: false
---

운영체제가 관리해야 할 자원은 크게 물리적인 자원과 추상적인 자원으로 구분할 수 있습니다.

운영체제에서 커널의 역할은 CPU나 메모리와 같은 물리적인 자원을 사용자에게 태스크와 세그먼트, 페이지와 같은 추상적인 자원으로 추상화 하여 사용자에게 인터페이스를 제공하는것입니다. 덕분에 우리는 메모리 레이아웃, 스케줄러, 네트워킹 등과 같은 시스템의 세부 구현 사항에 대한 이해 없이도 프로그램을 실행시킬 수 있습니다.

## 리눅스 커널의 구성요소

<div align=center>
	<img src="https://user-images.githubusercontent.com/28651727/142136168-764b3260-763b-42d5-8629-0af53dfd8a6c.png"/>
	<b>Figure 1 : Linux Kernel</b>
</div>
그림은 리눅스 커널 내부를 논리적인 구성 요소로 구분하여 그린 것입니다. 
커널의 Manager에 해당하는 요소들은 각각 Filesystem Manger는 디스크를 파일로, Memroy Manager는 메모리를 Page또는 Segment로, Task Manger는 CPU를 Task로, Network Manager는 네트워크 장치를 소켓으로, Device Manager는 block 또는 character로 물리적인 자원을 추상적인 자원으로써 제공해 줍니다.

<div align=center>
	<img src="https://linux-kernel-labs.github.io/refs/heads/master/_images/ditaa-f45246aade5ecc7cfb71f7f103a57f95fc7c2b9e.png" style="width : 600px">
	<img src="https://user-images.githubusercontent.com/28651727/142143439-9c028907-cc8b-457e-ae7d-7150913a2d8d.png" style="width : 600px">
</div>

<div align=center>
<b>Figure 2 : Linux Kernel Source Organization</b>
</div>

### kerenl
kernel 디렉토리에는 Figure 1의 Task Manager에 해당하는 부분이 구현된 디렉터리입니다. Task 생성 및 소멸, 스케줄링, 시그널 처리 등의 기능을 구현하고 있습니다.

### arch
arch 디렉토리에는 하드웨어에 종속적인 부분들을 구현한 디렉토리로 cpu의 타입에 따라 하위 디렉토리가 구성다. 대표적인 architecture인 x86, arm등을 여기서 확인할 수 있습니다.

x86을 기준으로 살펴보면 `./arch/x86/boot`에는 부팅시 사용하는 부트 스트랩 코드, `./arch/x86/kernel`에는 task manager의 문맥교환 및 스레드 관리, `./arch/x86/mm` page fault관련 처리 기능 등 하드웨어 종속적인 부분들이 구현되어있음을 확인할 수 있습니다.

### fs
fs 디렉토리에는 Figure 1의 Filesystem Manager에 해당하는 부분이 구현된 디렉터리 입니다. 리눅스에서 지원하는 ext2, ext3, ext4, f2fs등의 파일시스템이 구현되어 있으며 `open()`, `read()`, `write()`와 같은 파일관련 시스템 호출이 구현되어 있습니다

### mm 
Memory Manager가 구현된 디렉토리로물리메모리, 가상메모리 관리, Task마다 할당되는 메모리 객체관리 등의 기능이 구현되어 있습니다.

### driver
주변 장치를 추상화 하는 디바이스 드라이버가 구현된 디렉토리로 android, bluetooth, dma등 다양한 주변장치에 대한 드라이버가 구현되어있는것을 확인할 수 있습니다.

### net
Network Manager가 구현된 디렉토리로 802, ipv4,6, appletalk, bluetooth등 다양한 통신 프로토콜이 구현되어있는것을 확인할 수 있습니다.

이외에도 프로세스간 통신기능을 구현한 ipc디렉토리, 커널 초기화를 구현한 init디렉토리, 리눅스 커널의 헤더파일을 구현한 inlcude디렉토리, 이외 다양한 기능들을 구현한 others디렉토리가 있습니다. 


## Process manager(CPU)

커널은 System Call Interface(이하 SCI)를 통해 응용 프로그램에 인터페이스를 제공하여 프로세스 생성(fork(), exec(), ...), 프로세스 종료(kill(), exit()), 프로세스간의 통신 및 동기화(signal(),...)를 수행합니다.

실행중인 스레드들은 CPU를 공유해야 합니다. 리눅스의 경우에는 CFS(Completely Fair Scheduler) 스케줄러를 통해 이를 관리를 합니다. 시스템이 현재 프로세스를 계속 수행할 수 없는 상황이 되면 커널의 `schedule()`함수가 호출되고 그 내부의 `__schedule()`함수가 스케줄링의 핵심동작을 수행합니다.

```cpp
static void __sched notrace __schedule(bool preempt) {
	struct task_struct *prev, *next; // 프로세스의 속성 정보를 표현하는 자료구조
	unsigned long *switch_count;
	struct rq_flags rf;
	struct rq *rq;
	int cpu;

	cpu = smp_processor_id();
	rq = cpu_rq(cpu);
	prev = rq->curr; // 현재 실행중인 프로세스의 태스크 디스크립터 주소를  prev로

...

	next = pick_next_task(rq, prev, &rf); // 다음 실행할 프로세스의 태스크 디스크립터 주소를 next로
	clear_tsk_need_resched(prev);
	clear_preempt_need_resched();

	if (likely(prev != next)) { // prev와 next가 다른경우
		rq->nr_switches++;
		rq->curr = next; // 런큐 curr 필드에 저장합니다.

		++*switch_count; // context switch 횟수 정보를 담고있습니다.

		trace_sched_switch(preempt, prev, next);

		/* Also unlocks the rq: */
		rq = context_switch(rq, prev, next, &rf); // context switch를 수행합니다.
	} else {
		rq->clock_update_flags &= ~(RQCF_ACT_SKIP|RQCF_REQ_SKIP);
		rq_unlock_irq(rq, &rf);
	}

	balance_callback(rq);
}
```
