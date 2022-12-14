---
title: "[YCSB] Introduction"
date: 2021-07-18 16:21:13
category: 'OpenSource'
draft: false
---

YCSB(Yahoo! Cloud Serving Benchmark)는 NoSQL 데이터베이스의 쿼리 테스트를 위한 오픈소스 벤치마킹툴입니다.

### Load vs Run

YCSB에서의 `load`명령어는 데이터베이스에 데이터를 생성하며, `run`은 데이터베이스에서 가능한 명령어를 수행합니다. 즉, `load`명령은 insert 연산만을 수행하며, `run`의경우 YCSB의 workload에 따라 read, inserts, update를 수행합니다. 

## RocksDB

### Load
```
./bin/ycsb load rocksdb -s -P workloads/workloada -p rocksdb.dir=../data -p recordcount=10000
```

```
[OVERALL], RunTime(ms), 343  
[OVERALL], Throughput(ops/sec), 29154.51895043732  
[TOTAL_GCS_G1_Young_Generation], Count, 1  
[TOTAL_GC_TIME_G1_Young_Generation], Time(ms), 8  
[TOTAL_GC_TIME_%_G1_Young_Generation], Time(%), 2.3323615160349855  
[TOTAL_GCS_G1_Old_Generation], Count, 0  
[TOTAL_GC_TIME_G1_Old_Generation], Time(ms), 0  
[TOTAL_GC_TIME_%_G1_Old_Generation], Time(%), 0.0  
[TOTAL_GCs], Count, 1  
[TOTAL_GC_TIME], Time(ms), 8  
[TOTAL_GC_TIME_%], Time(%), 2.3323615160349855  
[CLEANUP], Operations, 1  
[CLEANUP], AverageLatency(us), 1102.0  
[CLEANUP], MinLatency(us), 1102  
[CLEANUP], MaxLatency(us), 1102  
[CLEANUP], 95thPercentileLatency(us), 1102  
[CLEANUP], 99thPercentileLatency(us), 1102  
[INSERT], Operations, 10000  
[INSERT], AverageLatency(us), 15.312  
[INSERT], MinLatency(us), 5  
[INSERT], MaxLatency(us), 36223  
[INSERT], 95thPercentileLatency(us), 19  
[INSERT], 99thPercentileLatency(us), 36  
[INSERT], Return=OK, 10000  
```
### Run
```
./bin/ycsb run rocksdb -s -P workloads/workloada -p rocksdb.dir=../data -p operationcount=10000
```
```
[OVERALL], RunTime(ms), 419  
[OVERALL], Throughput(ops/sec), 23866.34844868735  
[TOTAL_GCS_G1_Young_Generation], Count, 1  
[TOTAL_GC_TIME_G1_Young_Generation], Time(ms), 8  
[TOTAL_GC_TIME_%_G1_Young_Generation], Time(%), 1.9093078758949882  
[TOTAL_GCS_G1_Old_Generation], Count, 0  
[TOTAL_GC_TIME_G1_Old_Generation], Time(ms), 0  
[TOTAL_GC_TIME_%_G1_Old_Generation], Time(%), 0.0  
[TOTAL_GCs], Count, 1  
[TOTAL_GC_TIME], Time(ms), 8  
[TOTAL_GC_TIME_%], Time(%), 1.9093078758949882  
[READ], Operations, 4909  
[READ], AverageLatency(us), 4.262986351599103  
[READ], MinLatency(us), 1  
[READ], MaxLatency(us), 1720  
[READ], 95thPercentileLatency(us), 14  
[READ], 99thPercentileLatency(us), 26  
[READ], Return=OK, 4909  
[CLEANUP], Operations, 1  
[CLEANUP], AverageLatency(us), 1174.0  
[CLEANUP], MinLatency(us), 1174  
[CLEANUP], MaxLatency(us), 1174  
[CLEANUP], 95thPercentileLatency(us), 1174  
[CLEANUP], 99thPercentileLatency(us), 1174  
[UPDATE], Operations, 5091  
[UPDATE], AverageLatency(us), 18.371439795717933  
[UPDATE], MinLatency(us), 7  
[UPDATE], MaxLatency(us), 9679  
[UPDATE], 95thPercentileLatency(us), 37  
[UPDATE], 99thPercentileLatency(us), 80  
[UPDATE], Return=OK, 5091  
```

## MongoDB

### Load
```
./bin/ycsb load mongodb -s -P workloads/workloada -p mongodb.url="mongodb://127.0.0.1:27017/workload" -p recordcount=1000
```
url은 mongodb의 url을 입력하면 되며 뒤의 workloada는 workloada라는 이름의 데이터베이스를 만들어서 사용한다는 의미입니다.  
YCSB의 workload에 대해서는 https://github.com/brianfrankcooper/YCSB/wiki/Core-Workloads

```
[OVERALL], RunTime(ms), 519
[OVERALL], Throughput(ops/sec), 1926.7822736030828
[TOTAL_GCS_G1_Young_Generation], Count, 1
[TOTAL_GC_TIME_G1_Young_Generation], Time(ms), 11
[TOTAL_GC_TIME_%_G1_Young_Generation], Time(%), 2.119460500963391
[TOTAL_GCS_G1_Old_Generation], Count, 0
[TOTAL_GC_TIME_G1_Old_Generation], Time(ms), 0
[TOTAL_GC_TIME_%_G1_Old_Generation], Time(%), 0.0
[TOTAL_GCs], Count, 1
[TOTAL_GC_TIME], Time(ms), 11
[TOTAL_GC_TIME_%], Time(%), 2.119460500963391
[CLEANUP], Operations, 2
[CLEANUP], AverageLatency(us), 1629.5
[CLEANUP], MinLatency(us), 2
[CLEANUP], MaxLatency(us), 3257
[CLEANUP], 95thPercentileLatency(us), 3257
[CLEANUP], 99thPercentileLatency(us), 3257
[INSERT], Operations, 1000
[INSERT], AverageLatency(us), 525.652
[INSERT], MinLatency(us), 156
[INSERT], MaxLatency(us), 95423
[INSERT], 95thPercentileLatency(us), 616
[INSERT], 99thPercentileLatency(us), 758
[INSERT], Return=OK, 1000
```

### Run
```
./bin/ycsb run mongodb -s -P workloads/workloada -p mongodb.url="mongodb://127.0.0.1:27017/workload" -p operationcount=1000
```

```
[OVERALL], RunTime(ms), 459
[OVERALL], Throughput(ops/sec), 2178.649237472767
[TOTAL_GCS_G1_Young_Generation], Count, 1
[TOTAL_GC_TIME_G1_Young_Generation], Time(ms), 9
[TOTAL_GC_TIME_%_G1_Young_Generation], Time(%), 1.9607843137254901
[TOTAL_GCS_G1_Old_Generation], Count, 0
[TOTAL_GC_TIME_G1_Old_Generation], Time(ms), 0
[TOTAL_GC_TIME_%_G1_Old_Generation], Time(%), 0.0
[TOTAL_GCs], Count, 1
[TOTAL_GC_TIME], Time(ms), 9
[TOTAL_GC_TIME_%], Time(%), 1.9607843137254901
[READ], Operations, 475
[READ], AverageLatency(us), 577.9852631578948
[READ], MinLatency(us), 191
[READ], MaxLatency(us), 54559
[READ], 95thPercentileLatency(us), 658
[READ], 99thPercentileLatency(us), 976
[READ], Return=OK, 475
[CLEANUP], Operations, 2
[CLEANUP], AverageLatency(us), 1631.5
[CLEANUP], MinLatency(us), 2
[CLEANUP], MaxLatency(us), 3261
[CLEANUP], 95thPercentileLatency(us), 3261
[CLEANUP], 99thPercentileLatency(us), 3261
[UPDATE], Operations, 525
[UPDATE], AverageLatency(us), 354.56190476190477
[UPDATE], MinLatency(us), 188
[UPDATE], MaxLatency(us), 7471
[UPDATE], 95thPercentileLatency(us), 561
[UPDATE], 99thPercentileLatency(us), 698
[UPDATE], Return=OK, 525
```