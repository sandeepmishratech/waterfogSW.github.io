LSM-Tree와 B-Tree는 현대 Key-value 저장소의 스토리지 엔진으로 주로 사용되는 자료구조입니다. LSM-Tree의 경우 updata 쿼리에서, B-Tree는 좁은 범위의 lookups에서 좋은 성능을 보입니다.

## B-Trees
B-tree는 디스크에서 사용하기위해 설계된 **인덱스 구조**로 R. Bayer 와 E. McCreight가 1970년 7월 발표한 논문 "Organization and Maintenance of Large Ordered Indexes"에서 처음 발표되었습니다.

>**인덱스구조**  
> 데이터베이스에서 탐색속도를 높이는데 사용되는 자료구조입니다.  
> 인덱스 구조를 생성하고 유지하기 위해서는 추가적인 스토리지 공간과 쓰기작업과 관련된 비용이 발생합니다. 하지만 인덱스를 사용하여 쿼리하는것이 데이터베이스의 모든 행을 탐색하는것보다 훨씬 빠르기 때문에 이러한 비용은 큰문제가 되지 않습니다.

B-tree를 사용하는 가장 큰 이유는 디스크 접근시간을 줄일 수 있기 때문입니다.

트리 명령어(search, insert, delete, max, min,... etc)들은 O(height)의 디스크 접근시간을 요구합니다. Btree

## LSM-Trees
Log-Structured Merge Tree는 Patrick O’Neil이 1996년 발표한 논문 The Log-Structured Merge-Tree (LSM-Tree)에서 처음 발표되었습니다. LSM-tree는 우수한 업데이트 성능과 space amplification특성때문에 많은 key-value store 데이터베이스에서 차용되었습니다.

> Space amplification
> 

LSM-tree의 update는 디스크 I/O를 최소화하기위해 메모리의 버퍼에 log를 남깁니다. 그리고 버퍼는 주기적으로 정렬되어 디스크에 쓰여지는데 이를 `flush`라고 하며, 정렬된 일련의 배열을 `run`이라고 합니다.
