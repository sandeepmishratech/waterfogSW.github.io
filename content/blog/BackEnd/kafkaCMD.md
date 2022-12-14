---
title: "[Kafka] Kafka 커맨드라인 툴"
date: 2021-08-15 16:21:13
category: 'BackEnd'
draft: false
---

## 토픽 생성

`kafka-topics.sh`를 통해 토픽 관련 명령을 실행할 수 있다. 

```
$ bin/kafka-topics.sh \
--create \
--bootstrap-server <카프카 서버 주소>:9092 \
--partitions 3 \
--replication-factor 1 \
--config retention.ms=172800000 \
--topic hello.kafka
```

1. create
   - 토픽을 생성하는 명령어
2. bootstrap-server
   - 토픽을 생성할 카프카 클러스터를 구성하는 브로커 IP와 Port를 적는다.
3. partitions
   - 파티션의 개수를 지정한다. 옵션을 명시하지 않으면, 카프카 브로커의 설정파일의 `num.partitions`옵션을 따른다.
4. replication-factor
   - 토픽의 파티션을 복제할 복제 개수를 적는다. 사용하는 브로커의 개수를 따르면 되고, 실제 업무환경에서는 3개 이상의 카프카 브로커로 운영하는 것이 일반적
5. config retentions.ms
   - 토픽의 데이터를 유지하는 기간을 나타낸다. ms단위로, 172800000은 2일을 나타낸다. 따라서 2일이 지난 토픽 데이터는 삭제된다.

## 토픽 리스트 조회

### 토픽 리스트 조회**
```
$ bin/kafka-topics.sh --bootstrap-server <카프카 서버 주소>:9092 --list
```

### 상세 조회

```
$ bin/kafka-topics.sh --bootstrap-server <카프카 서버 주소>:9092 --describe --topic <조회할 topic명>
```

```
Topic: hello.kafka      TopicId: XGxv2fB5QTidTzfb5rsShA PartitionCount: 3       ReplicationFactor: 1    Configs: segment.bytes=1073741824,retention.ms=172800000
        Topic: hello.kafka      Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 2    Leader: 0       Replicas: 0     Isr: 0
```

## 토픽 옵션 수정

토픽 옵션을 변경하기 위해서 `kafka-topics.sh` 또는 `kafka-configs.sh`를 사용한다.


### 파티션 개수 변경
```
$ bin/kafka-topics.sh \
--topic <속성을 변경할 topic의 이름> \
--bootstrap-server <카프카 서버 주소>:9092 \
--alter \
--partitions 4
```

파티션의 개수는 늘릴수는 있지만 줄일 수는 없다. 따라서 파티션 개수를 늘릴때는 반드시 늘려야 하는 상황인지 판단하는것이 중요하다.

```
Topic: hello.kafka      TopicId: XGxv2fB5QTidTzfb5rsShA PartitionCount: 4       ReplicationFactor: 1    Configs: segment.bytes=1073741824,retention.ms=172800000
        Topic: hello.kafka      Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 2    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 3    Leader: 0       Replicas: 0     Isr: 0
```
파티션의 번호는 0부터 1씩 늘어나며 순차적으로 부여된다. 

### 데이터 유지시간(retention.ms) 변경

```
$ bin/kafka-configs.sh --bootstrap-server <카프카 서버 주소>:9092 \
--entity-type topics \
--entity-name hello.kafka \
--alter --add-config retention.ms=86400000
```

**변경 확인**

```
$ bin/kafka-topics.sh --bootstrap-server 52.79.85.44:9092 --describe --topic hello.kafka

Topic: hello.kafka      TopicId: XGxv2fB5QTidTzfb5rsShA PartitionCount: 4       ReplicationFactor: 1    Configs: segment.bytes=1073741824,retention.ms=86400000
        Topic: hello.kafka      Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 2    Leader: 0       Replicas: 0     Isr: 0
        Topic: hello.kafka      Partition: 3    Leader: 0       Replicas: 0     Isr: 0
```

## kafka-console-producer.sh

`kafka-console-producer.sh`명령어를 통해 토픽에 데이터를 넣을 수 있다. 

토픽에 넣는 데이터는 `레코드(record)`라고 부르며 메시지 키(key)와 값(value)로 이루어져 있다.

### 메시지 키 없이 메시지 값만 보내기

메시지 키없이 메시지 값만 보낼경우 메시지 키가 자바의 null로 설정되어 브로커로 전송된다.
```
$ bin/kafka-console-producer.sh --bootstrap-server <카프카 서버 주소>:9092 \
--topic hello.kafka

>NullTest1
>NullTest2
>NullTest3
```

`bin/kafka-console-producer.sh`를 통해 전송되는 레코드의 값은 UTF-8을 기반으로 Byte로 변환된 후 ByteArraySerializer로만 직렬화 된다. 즉, String이 아닌 타입으로는 직렬화 하여 전송할 수 없다. 

만약 다른 타입으로 직렬화하여 데이터를 브로커로 전송하고 싶으면 직접 프로듀서 애플리케이션을 개발하여야 한다.

메시지 키가 null인 경우 프로듀서가 파티션으로 전송할때 레코드 배치 단위로 라운드로빈으로 전송된다.

### 메시지 키를 가지는 레코드 전송

```
$ bin/kafka-console-producer.sh --bootstrap-server <카프카 서버 주소>:9092 \
--topic hello.kafka \
--property "parse.key=true" \
--property "key.separator=:"

>key1:val1
>key2:val2
>key3:val3
>key3:val4
```

- key.separator=:
  - 키와 값을 나누는 구분자를 선언한다. 만약 구분자를 넣지 않고 엔터를 누르면 KafkaException과 함께 종료된다.

메시지 키와 값을 함께 전송한 레코드는 메시지 키의 해시값을 작성하여 존재하는 토픽의 파티션중 한개에 할당된다. 따라서 메시지 키가 동일할 경우 동일 파티션에 전송된다.

## kafka-console-consumer.sh

`kafka-console-consumer.sh`명령어를 통해 토픽에 데이터를 확인할 수 있다.

```
$ bin/kafka-console-consumer.sh --bootstrap-server <카프카 서버 주소>:9092 \
--topic hello.kafka \
--from-beginning

NullTest2
val1
val2
NullTest3
val3
val4
NullTest1
^CProcessed a total of 7 messages
```

- from-beginning
  - 토픽에 저장된 가장 처음의 데이터부터 출력한다.

**키와 함께 확인**
```
$ bin/kafka-console-consumer.sh --bootstrap-server <카프카 서버 주소>:9092 \
--topic hello.kafka \
--property print.key=true \
--property key.separator="-" \
--group hello-group \
--from-beginning

null-NullTest2
key1-val1
key2-val2
null-NullTest3
key3-val3
key3-val4
null-NullTest1
^CProcessed a total of 7 messages
```

- group hello-group
  - group옵션을 통해 신규 컨슈머 그룹 hello-group을 생성하였다.
  - 컨슈머 그룹은 1개 이상의 컨슈머로 이루어져 있으며 해당 컨슈머 그룹을 통해 가져간 토픽 메시지는 가져간 메시지에 대해 커밋을 한다
  - 커밋이란 컨슈머가 특정 레코드까지 처리를 완료했다고 레코드의 오프셋 번호를 카프카 브로커에 저장하는 것이다. 
  - 커밋 정보는 `__consumer_offsets` 이름의 내부 토픽에 저장된다.

- from-beginning
  - 전송한 데이터의 순서가 현재 출력되는 순서와 다른것을 볼 수 있다. 이는 카프카의 파티션때문에 생기는 현상이다.
  - 만약 토픽에 넣은 데이터의 순서를 보장하고 싶다면 파티션 1개로 구성된 토픽을 만드는 것이다.
  - 한개의 파티션에서는 데이터의 순서를 보장하기 때문이다.

## kafka-consumer-groups.sh

**생성한 컨슈머 그룹 리스트**
```
$ bin/kafka-consumer-groups.sh --bootstrap-server <카프카 서버 주소>:9092 --list
hello-group
```

**컨슈머 그룹이 데이터를 가져가는 토픽 확인**
```
$ bin/kafka-consumer-groups.sh --bootstrap-server <카프카 서버 주소>:9092 \
--group hello-group \
--describe

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
hello-group     hello.kafka     2          3               3               0               -               -               -
hello-group     hello.kafka     1          3               3               0               -               -               -
hello-group     hello.kafka     0          1               1               0               -               -               -
```

## kafka-verifiable-producer, consumer.sh

`kafka-verfiable-producer, consumer.sh`을 사용하면 String타입 메시지 값을 코드없이 주고받을수 있어 네트워크 통신 테스트를 할때 유용하다.


**데이터 전송**
```
$ bin/kafka-verifiable-producer.sh --bootstrap-server 3.35.13.39:9092 \
--max-messages 10 \
--topic verify-test

{"timestamp":1629028682306,"name":"startup_complete"}
[2021-08-15 20:58:02,510] WARN [Producer clientId=producer-1] Error while fetching metadata with correlation id 1 : {verify-test=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)
{"timestamp":1629028682746,"name":"producer_send_success","key":null,"value":"0","offset":0,"topic":"verify-test","partition":0}
{"timestamp":1629028682748,"name":"producer_send_success","key":null,"value":"1","offset":1,"topic":"verify-test","partition":0}
{"timestamp":1629028682748,"name":"producer_send_success","key":null,"value":"2","offset":2,"topic":"verify-test","partition":0}
{"timestamp":1629028682749,"name":"producer_send_success","key":null,"value":"3","offset":3,"topic":"verify-test","partition":0}
{"timestamp":1629028682749,"name":"producer_send_success","key":null,"value":"4","offset":4,"topic":"verify-test","partition":0}
{"timestamp":1629028682750,"name":"producer_send_success","key":null,"value":"5","offset":5,"topic":"verify-test","partition":0}
{"timestamp":1629028682750,"name":"producer_send_success","key":null,"value":"6","offset":6,"topic":"verify-test","partition":0}
{"timestamp":1629028682750,"name":"producer_send_success","key":null,"value":"7","offset":7,"topic":"verify-test","partition":0}
{"timestamp":1629028682750,"name":"producer_send_success","key":null,"value":"8","offset":8,"topic":"verify-test","partition":0}
{"timestamp":1629028682750,"name":"producer_send_success","key":null,"value":"9","offset":9,"topic":"verify-test","partition":0}
{"timestamp":1629028682757,"name":"shutdown_complete"}
{"timestamp":1629028682759,"name":"tool_data","sent":10,"acked":10,"target_throughput":-1,"avg_throughput":22.07505518763797}
```

**전송한 데이터 확인**
```
$ bin/kafka-verifiable-consumer.sh --bootstrap-server 3.35.13.39:9092 \
--topic verify-test \
--group-id test-group

{"timestamp":1629028804932,"name":"startup_complete"}
{"timestamp":1629028805183,"name":"partitions_assigned","partitions":[{"topic":"verify-test","partition":0}]}
{"timestamp":1629028805250,"name":"records_consumed","count":10,"partitions":[{"topic":"verify-test","partition":0,"count":10,"minOffset":0,"maxOffset":9}]}
{"timestamp":1629028805260,"name":"offsets_committed","offsets":[{"topic":"verify-test","partition":0,"offset":10}],"success":true}
...
```

## kafka-delete-records.sh

`kafka-delete-records.sh`명령어를 통해 이미 적재된 토픽의 데이터를 지울 수 있다. 토픽의 데이터중 가장오래된 데이터(가장 낮은 오프셋)부터 특정 시점의 오프셋가지 삭제할 수 있다.

이때 삭제하고자 하는 데이터에 대한 정보를 파일(.json)로 저장해서 사용해야 한다.
```
$ vi delete-topic.json
{"partitions": [{"topic": "verify-test", "partition": 0, "offset": 5}], "version":1}

$ bin/kafka-delete-records.sh --bootstrap-server 3.35.13.39:9092 \
--offset-json-file delete-topic.json

Executing records delete operation
Records delete operation completed:
partition: verify-test-0        low_watermark: 5
```

여기서 주의해야 할 점은 토픽의 특정 레코드 하나만 삭제되는것이 아니라 파티션에 존재하는 가장 오래된 오프셋부터 지정한 오프셋까지 삭제된다는 점이다.
즉, 카프카는 토픽의 특정 데이터만 삭제할 수 없다.

