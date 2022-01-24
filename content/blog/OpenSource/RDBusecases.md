---
title: "[RocksDB] RocksDB 활용사례"
date: 2021-07-27 16:21:13
category: 'OpenSource'
draft: false
---

<br>

<div align=center>
<div class="card">
  <div class="card__image">
    <img class="image" src="https://user-images.githubusercontent.com/28651727/124079851-a09b4280-da84-11eb-87c1-c8e525739628.png"/>
  </div>
</div>
</div>

데이터 처리속도와 처리량의 한계로 인해 전통적인 RDBMS 데이터베이스와 다른 새로운 데이터베이스가 요구되었습니다. 이로인해 NoSQL(Not only SQL)과 NewSQL과 같은 데이터 베이스들이 등장하였습니다.

## RocksDB의 특징
- LSM-tree를 기반으로하여 대용량의 데이터를 저장하는데(write intensive workload)에 적합합니다
- InnoDB와 비교하였을때 RocksDB는 쓰기성능에서의 압도적인 성능차이를 보였고, 읽기성능에서는 조금 뒤떨어지는 성능을 보여주었지만 쓰기성능에서의 이점을 상쇄할 만큼의 큰 차이는 보이지 않았습니다.
![image](https://user-images.githubusercontent.com/28651727/127099410-8fde9625-df3b-4619-a432-d1a4aaf5f2a2.png)  
(출처 : https://smalldatum.blogspot.com/2017/11/insert-benchmark-io-bound-intel-nuc.html)
- RocksDB는 다양한 parameter를 제공하여 여러 하드웨어 configuration에서도 좋은 성능을 내도록 할 수 있습니다.

Facebook은 방대한 MySQL클러스터의 스토리지 엔진을 InnoDB에서 RocksDB로 변경하는 작업을 2018년에 완료하였는데, 덕분에 50%에 가까운 저장용량을 아낄 수 있었습니다. 

## Who Uses RocksDB
RocksDB는 점점더 많이 활용되고 있지만 그중 대표적인 활용사례를 몇가지 추려 보았습니다. 

**분산데이터 베이스에서의 스토리지 엔진으로써의 활용**
- Apache Cassandra
- CockroachDB
- MySQL(MyRocks)
- Rockset

**분산데이터 베이스 외에서의 활용**
- **Kafka Streams**
- Apache Samza
- **Netflix**
- Santander UK
- **Uber**

### Netflix
넷플릭스는 유저데이터를 제공하는데 지연시간을 단축시키기 위해 **EVCache**를 사용하는데, 이때 RocksDB를 사용합니다.

![image](https://user-images.githubusercontent.com/28651727/127105880-0417254d-2971-407d-82b0-2aed10b79b42.png)  
먼저 유저가 넷플릭스에 로그인 하면 넷플릭스 서버는 데이터베이스로부터 유저의 시청기록, 평가기록, 개인 데이터들을 기반으로한 추천 영상등과 같은 유저데이트를 데이터베이스 서버로부터 가져오게됩니다. 하지만 이러한 유저데이터는 너무 방대하기때문에 클라우드 DB만을 사용하기에는 지연시간이 길어집니다. 이러한 지연시간을 단축시키기 위해 넷플릭스는 EVCache라는 캐시를 설계하였습니다. 

![image](https://user-images.githubusercontent.com/28651727/127106247-d27b69fe-06ce-4b28-9882-ad76209bc84d.png)  
유저데이터가 요청되면, 먼저 EVCache를 확인하여 유저데이터가 있는지 확인하고, 없다면(Cache miss) Similar Service를 통해 아마존 SimpleDB와 같은 클라우드 데이터베이스에 접근하여 유저데이터를 가져옵니다. 가져온 유저데이터는 EVCache에 쓰여지고, 사용자에게 제공됩니다.

![image](https://user-images.githubusercontent.com/28651727/127106521-b07faa6b-a13e-45a5-87a6-39224d6c29eb.png)  
넷플릭스의 EVCache 클러스터는 기존에는 메모리만을 활용하였지만, 이는 메모리의 비용적 문제로 확장성(scalable)이 떨어지고 hardware fault에 취약하였습니다.

넷플릭스의 EVCache cluster는 지역별로 존재하는데 한국에 존재하는 사용자는 주로 한국의 EVCache에 접근하고 미국이나 영국의 EVCache에는 접근하지 않습니다. 이러한 이유로 한국의 EVCache입장에서 한국의 유저데이터는 Hot한 유저데이터가 되지만, 미국, 영귝의 EVCache입장에서 한국의 유저데이터는 Cold한 유저데이터가 됩니다. 

이러한 차이로 모든 유저데이터를 메모리상에 저장하는것은 비효율적입니다. 

![image](https://user-images.githubusercontent.com/28651727/127107542-49e39cf0-58c5-4984-b370-737100c69269.png)  
넷플릭스는 효율적으로 유저데이터를 관리하기 위해 캐시를 메모리를 활용하는 L1 Cache, SSD를 활용하는 L2 Cache로 나누었습니다. 그중 L2 Cache에서 **RocksDB**가 사용됩니다.

L1,L2 캐시는 각각 AWS EC2 인스턴스의 r3, i2인스턴스를 사용합니다. SSD를 사용함으로써 저장할 데이터의 용량은 25배 이상 늘렸지만, 시간당 가격은 2.5배 밖에 차이가 나지 않습니다.

### Kafka Streams
Kafka에서는 스트림 프로세싱(stream processing)의 기본 상태저장소로(state store) rocksDB를 사용합니다.

**스트림 프로세싱**은 이동 중인 데이터, 즉 데이터가 생성되거나 수신되는 즉시 데이터 위에 컴퓨팅되는 데이터를 처리하는 것입니다.

대부분의 데이터는 센서 이벤트, 웹 사이트의 사용자 활동, 금융 거래 등과 같은 연속적인 스트림으로 생성됩니다. 이 모든 데이터는 시간이 지남에 따라 일련의 이벤트로 생성됩니다.

![image](https://user-images.githubusercontent.com/28651727/127112312-183fd6be-68f1-4d87-8554-8b09d8498296.png)

**스트림프로세싱이 도입되기 전**, 데이터는 데이터베이스, 파일 시스템 또는 다른 형태의 대용량 저장소에 저장되었고 애플리케이션은 필요에 따라 데이터를 쿼리하거나 각종 분석작업을 진행하는 배치 처리의 형태로 진행되었습니다.

![image](https://user-images.githubusercontent.com/28651727/127112468-5dc1bcaf-cfb6-48d9-bb1a-6c848be49a06.png)

스트림 프로세싱은 각 구간의 스트림 프로세서(Stream processor)를 통해 데이터를 실시간으로 처리합니다. 

데이터 스트림을 수신 및 전송하고 애플리케이션 또는 분석 로직을 실행하는 시스템을 스트림 프로세서(Stream processor)라고 합니다.

![image](https://user-images.githubusercontent.com/28651727/127112903-3ee4239f-31f6-4073-817b-cadf0c117a59.png)

스트림을 처리 하다보면 이전 스트림프로세서가 처리한 결과(state)를 참조해야 하는 경우가 있습니다. 이런 류의 처리 방식을 상태 기반처리라고 합니다. 상태 기반 스트림 처리를 하기 위해서는 애플리케이션의 처리 결과를 저장할 상태 저장소(state store)가 필요합니다. 이때 스트림 프로세싱 애플리케이션이 이 저장소를 관리하면 내부 상태 저장소라고 하고, Database와 같은 별도의 상태 저장소를 사용하게 되면 외부 상태 저장소라고 합니다.

Kafk Streams에서는 RocksDB를 기본 상태저장소로 사용합니다. 여기서 RocksDB는 in-memory key-value store와 달리 디스크를 활용할 수 있어 가용메모리보다 큰 state를 저장할 수 있게 해줍니다.

### Uber

우버에서는 Cherami라는 MessageQueue System에 RocksDB를 사용합니다. 

![image](https://user-images.githubusercontent.com/28651727/127115290-105a9989-0b30-4402-af51-32b9ac8195f4.png)

Uber는 기존에는 redis라는 in-memory key-value데이터베이스를 사용하였는데 Uber가 필요로하는 확정성과 지속성을 제공해 주지못해 RcoksDB로 데이터베이스를 변경하였다 라고 설명합니다. 

Uber Message Queue system에서 RocksDB는 메시지를 저장하는데 사용되는데 다음과 같이 Key를 높은 비트에 delivery time, 낮은 bit에 sequnce number로 하여 메시지를 저장하는 형태입니다.

![image](https://user-images.githubusercontent.com/28651727/127115733-f03a9b6c-5b3e-4a94-a2b2-2f4821bf3dd9.png)

## Reference  
- ROCKSET : https://rockset.com/blog/rocksdb-is-eating-the-database-world/  
- Ververica : https://www.ververica.com/what-is-stream-processing
- Uber : https://eng.uber.com/cherami-message-queue-system/