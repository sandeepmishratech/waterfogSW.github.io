---
title: '[OS] Log-Structured File system'
date: 2021-07-06 16:21:13
category: 'ComputerScience'
draft: false
---

<div align=center>
<div class="card">
  <div class="card__image">
    <img class="image" src="https://user-images.githubusercontent.com/28651727/124206229-ec4dfa80-db1d-11eb-8eee-191d50436808.png"/>
  </div>
</div>

<b>Mendel Rosenblum, author of The Design and Implementation of a Log-Structured File system</b>

</div>

LFS는 많은 synchronous random write를 작은 asynchronous sequential write로 전환하여 raw disk bandwidth를 100% 활용 하는것이 기본 개념입니다.

LFS는 쓰기 요청을 세그먼트 버퍼에 모으고 세그먼트 단위로 디스크에 Sequential write하여 Random write에서도 최적의 성능을 보여줍니다. 하지만 이러한 세그먼트를 활용하는 특성때문에 세그먼트 할당을 위한 공간을 확보하기 위한 Segment cleaning을 수행해야 합니다. 

## LFS의 등장 배경(Background)
### Technology Trends
- CPU의 속도가 크게 증가한데 비해 디스크의 접근속도의 향상은 미미했습니다.
- 메모리의 용량이 증가함에 따라 읽기 요청을 효과적으로 처리할 수 있게 되었고, 쓰기 요청이 부각되었습니다.
- 디스크 기술 또한 비약적으로 증가하였지만 들어가는 비용에 비해 성능이 크게 증가하지 못했습니다.

### Workloads Trends
- 일반 사무실과 공장은 작은 파일에대한 접근을 주로 하는 경향이 있습니다.
- 메인 메모리의 크기 향상은 더많은 파일 캐싱을 가능하게 하였고, 이로인해 읽기보다는 쓰기에 대한 Disk traffic이 부각되었습니다.
 
## LFS - Purpose
- FFS가 read performance에 목표를 두었다면, LFS는 write performane를 향상시키는것을 목표로 합니다.
- LSF에서는 작은 파일 접근의 효율화에 집중하고 있으며, 큰 파일의 경우 하드웨어의 대역폭 향상에 의존합니다.

## LFS - Design
- 메모리에 충분한 데이터가 모일때까지 버퍼링하였다가 버퍼링된 `segment`를 한번에 disk에 씁니다.
  - 메모리의 휘발성으로 인한 이슈가 존재합니다.
- Overwrite를 하지않고, Old copy가 앞에 남아있는 상태로 뒤에 공간을 할당합니다(out place update)
- 새로운 위치에 write하고 포인터를 바꾸기때문에 old file에 대한 GC(Garbage Collection)이 필요합니다.

### Inode

![image](https://user-images.githubusercontent.com/28651727/124464751-8ab4b700-ddcf-11eb-847f-a685571a3d2b.png)

위의 그림에서 배열 b[0]에 대해 수정이 발생하여 기존 데이터 블럭을 수정하지 않고 새로운 디스크 블럭 $D_{k,0}$ 를 쓰고, 마찬가지로 기존 inode를 수정하지 않고 새로운 inode를 생성하여 b[0]가 새로운 디스크 블럭 주소 위치를 가리키게 합니다.

$$E = mc^2$$


### Inode map : LFS에서의 Inode는 log에 쓰여저 고정된 위치에 존재하지 않습니다

- LFS의 Out place update라는 특성으로 인해 inode는 고정된 위치에 존재하지 않습니다. 따라서 LFS는 각 inode의 위치를 파악하기 위해서 `inode map`이라는 자료구조를 사용합니다. 
- `inode map`은 inode number를 입력값으로 가장 최근버전의 inode의 disk address를 출력합니다. 따라서 매번 inode가 디스크에 쓰여질때마다 imap또한 update되어 새로운 위치에 쓰여지게 됩니다
<img src="https://user-images.githubusercontent.com/28651727/124561385-cbb4d600-de78-11eb-93cb-24c0f555fb3c.png"/>
- 위의 그림과 같이 모든 새로운 정보를 쓰는 작업이 끝난 위치 바로 뒤에 imap이 쓰여지게 됩니다.

### Check point region
- imap 또한 위치가 고정되지 않고 디스크의 여러곳에 위치하게 됩니다. 결국 파일시스템은 파일의 look up을 시작하기위해 정보를 담고있는 고정된 위치가 필요합니다.
- LFS는 check-point region(CR)이라는 고정된 위치에 가장 최근의 inode map의 정보를 저장하고 CR을 확인함으로써 imap의 위치를 파악할 수 있습니다.
- CR은 주기적(ex, 30초)으로 업데이트 되기 때문에 성능에 나쁜영향을 주지 않습니다.
- 정리하자면 CR은 imap에 대한 위치정보를 imap은 inode에 대한 위치정보를 inode는 파일에대한 위치정보를 담고 있습니다.

![image](https://user-images.githubusercontent.com/28651727/124563519-10417100-de7b-11eb-8aea-cb7f7b8293f9.png)

### Directory

inode의 위치가 변경될 수 있지만 변경 사항은 디렉토리 자체에 반영되지는 않습니다. 대신 디렉토리가 동일한 `이름 : inode 번호` mapping을 가지고 있는 동안 imap 구조가 업데이트됩니다. 따라서 간접적으로 LFS는 재귀 업데이트 문제를 방지합니다.

### Segement
![image](https://user-images.githubusercontent.com/28651727/124404280-498fb900-dd75-11eb-9dd8-371b7e7d5882.png)

- 시간이 지나 log가 디스크의 끝에 도달하게 되면 파일이 삭제되고 덮어쓰기됨에 따라 가용공간이 여러 작은 조각들로 단편화 됩니다. 
- LFS에서는 이를 Threading과 Copying으로 해결합니다. 
  - Threading은 유효한 데이터들을 포인터들을 통해 연결시키는 방식입니다. 하지만 이러한 threading은 더많은 가용공간을 단편화시켜 큰 순차쓰기를 불가능하게 하고 이로인해 LFS는 기존 파일시스템에 비해 느려지게 됩니다.
  - Copying은 유효한 데이터를 Copy하여 한군데로 모아 큰 가용공간을 확보하는 방법입니다. 하지만 이러한 방법은 비용이 큽니다.
- Sprinte LFS는 Thread와 Copy두가지 방법을 모두 사용합니다. 
  - 먼저 디스크를 큰 고정크기의 segment들로 나눕니다.
  - 각 segment에서는 처음부터 끝까지 순차적으로 쓰입니다.
  - 모든 유효 데이터는 segment가 다시 쓰여지기 전에 다른 segment에 copy됩니다.
  - 하지만 로그는 순차적으로 쓰여야 하므로 segment끼리 thread 되어 연결됩니다.
- Segment의 크기는 충분히 크게잡아 disk의 bandwidth를 모두 활용할 수 있도록 합니다.

### Garbage Collection (Segement Cleaning)
![image](https://user-images.githubusercontent.com/28651727/124466193-57732780-ddd1-11eb-9e83-3cfea927ff31.png)
- 데이터를 수정할때 LFS는 기존의 데이터위치에 쓰지 않고 디스크의 새로운 위치에 데이터를 씁니다. 이때 기존의 데이터는 garbage가됩니다.
- M개의 현재 존재하는 Segment를 compact하여 N개의 Segment로 만들고(N < M), 디스크의 N개의 Segment에 쓰기작업을 합니다. 이후 M개의 오래된 segment는 지워지고 후속 쓰기작업을 위해 사용될 수 있습니다.
  - 각 Segment는 마지막 쓰기작업을 기준으로 분류됩니다.
  - Segment cleaner는 백그라운드에서 동작합니다.

### Determine Block Liveness
```
# A : address location of inode
# N : inode number
# T : offset
(N,T) = SegmentSummary[A];
inode = Read(imap[N]);
if (inode[T] == A) 
  // block D is alive
else
  // block D is garbage
```
- LFS는 block이 유효한지 판단하기 위해 해당 블럭의 위치 A를 SegmentSummary block을 통해 조회합니다. 
- SegmentSummary block에서 inode번호 N과 offset T를 확인하고 imap을 통해 유효한 N의 위치를 알아내고 N을 읽습니다.
- 마지막으로 inode를 통해 파일의 T번째 블록의 위치를 알아내고 만약 A의 주소값과 일치한다면 live block, 일치하지 않으면 garbage block으로 판단합니다.

LFS는 파일이 수정되거나 삭제되면 imap의 version number를 증가시켜 기록합니다. 또 disk의 segment에도 version number를 기록하여 imap의 version number와 disk의 version number를 비교하여 추가적인 읽기 작업 없이 블럭의 liveness를 판단합니다.

### Policy : Which Blocks To Clean
언제 GC를 수행할지는 시스템의 유휴시간 또는 주기적으로 하면 되기때문에 어려운 문제가 아니지만, 어떤 블럭을 clean할지는 결정하기 어려운 문제입니다.

#### Determining by Hot & Cold
Hot 세그먼트의 경우 자주 overwrite되기 때문에 오랫동안 기다렸다가 유휴공간이 많이 나오면 clean하는것이 좋습니다. 반면 Cold 세그먼트의 경우 유효 블럭의 개수가 많이 남아있을 확률이 높습니다. 따라서 LFS에서는 Hot세그먼트는 천천히 Cold세그먼트는 비교적 빠르게 GC을 수행합니다.

### Log : Crash Recovery
LFS의 버퍼는 세그먼트에 쓰기작업을 한후 디스크에 세그먼트를 쓰는 작업을 합니다. Checkpoint Region(CR)은 세그먼트의 시작부분과 끝부분을 가리키고 각 세그먼트는 다음 쓰일 세그먼트를 가리킵니다. 이러한 segment쓰기 작업이나 CR쓰기작업시 충돌이 발생할 수 있습니다. 

#### CR 쓰기작업에서의 충돌 해결
LFS에서는 CR업데이트를 원자적으로 수행하기 위해서 두개의 CR을 디스크의 양 끝단에 배치하고 번갈아 쓰기작업을 합니다. 그리고 타임스템프와 함께 헤더에 쓰고 CR부분에 쓰고 다시 타임스템프와 함께 마지막 블록에 써서 조심스럽게 CR을 업데이트 합니다. 만약 시스템 충돌이 발생할 경우 타임 스템프를 비교하여 시스템 충돌을 감지하고 가장 최근의 일관성있는 CR을 선택하여 일관성있는 CR쓰기작업을 가능하게 합니다.

#### Segment 쓰기작업에서의 충돌 해결
LFS는 30초 정도마다 CR을 쓰기 때문에 파일 시스템의 일관성 있는 마지막 스냅샷은 오래되었을 확률이 높습니다. 따라서 재부팅할 때 LFS는 체크포인트 영역, 가리키는 imap, 이후 파일 및 디렉토리만 읽어도 쉽게 복구할 수 있지만 마지막 몇 초 동안의 업데이트는 손실됩니다.

이를 개선하기 위해 LFS는 데이터베이스 커뮤니티에서 롤포워드(rollforward)라는 기술을 통해 이러한 세그먼트 중 많은 부분을 복구합니다. 기본 아이디어는 마지막 체크포인트 영역부터 시작하여 로그의 끝 부분(CR 내부에 존재)을 찾은 다음 이를 사용하여 끝까지 읽습니다. 그리고 다음 세그먼트에서 유효한 업데이트가 있는지 확인하고, 있는 경우 LFS는 파일 시스템을 업데이트하여 마지막 체크포인트 이후 작성된 많은 데이터와 메타데이터를 복구합니다.

**그림 출처**
Operating Systems: Three Easy Pieces, Remzi H. Arpaci-Dusseau and Andrea C. Arpaci-Dusseau (University of Wisconsin-Madison)