---
title: "[MongoDB] Introduction"
date: 2021-07-12 16:21:13
category: 'DB'
draft: false
---

title: "[MongoDB] GridFS"
<br>
> MongoDB의 [공식문서](https://docs.mongodb.com/manual/introduction/#key-features)를 참고하여 작성하였습니다.

MongoDB는 레코드를 BSON(Binary JSON)형태의 Document를 사용하는 NoSQL데이터베이스입니다.

## Document Database

### SQL Record
```
Name    age  number      satus
san     25   9273723723  A
```

### MongoDB Document
```json
{
    name: "san",
    age: 25,
    status: "A",
}
```
- Key : Value 형태
  - 여기서 name, age, status는 field이며 옆의 값들은 value로 field:value의 형태로 되어있습니다. 

- Native data types
  - Document의 데이터 타입은 다양한 프로그래밍 언어의 native data types을 지원합니다.

- Embedded documents & arrays
  - 각 필드의 값들은 document, arrays, arrays of documents도 될 수 있습니다. 
  - 이렇게 document내에 document 또는 array가 구성되어있는것을 `Embedded documents`, `Embedded array`라고 하는데 이러한 Embed(삽입)를 잘 활용하면 오버헤드가 큰 join연산을 줄일 수 있습니다

- Dynamic schema supports
  - 동적 스키마(Document, Collection)를 지원하여 다형성을 지원합니다. 동적 스키마란 Collection내의 문서들이 여러 형태의 데이터 타입을 지원하는것을 의미합니다.

### Collection/Views/On-Demand Materialized Views

MongoDB는 document들을 `collection`에 저장합니다. collection은 RDBMS의 table과 비슷한 개념입니다. 

추가적으로 다른 collection이나 view로 부터 Read-only View나 On-Demand Materialized Views를 만드는것을 지원합니다.

## Key Features

### High Performance
- 임베디드 데이터 모델(Embedded documents, arrays)을 통해 데이터 베이스 시스템에서의 I/O횟수를 줄입니다
- 인덱스를 통한 빠른 쿼리를 제공합니다.

### Rich Query Language
- 필터링, 수집, 정렬, 정규표현식 등 다양한 종류의 쿼리문을 지원합니다.

### High Availability
- 같은 그룸의 여러 MongoDB서버에 데이터 복제본을 저장하여 Redundancy를 제공하며 데이터 가용성을 높입니다. 

### Horizontal Scalability
- [Sharding](https://docs.mongodb.com/manual/sharding/#std-label-sharding-introduction)을 통해 높은 데이터 처리량과 매우 큰 데이터에 대한 배포를 지원합니다.

### Support for Multiple Storage Engines
- MongoDB는 다음의 [두가지 스토리지 엔진](https://docs.mongodb.com/manual/core/storage-engines/)을 제공합니다
  - WiredTiger Storage Engine (including support for Encryption at Rest)
  - In-Memory Storage Engine.

## RDBMS vs MongoDB

| RDBMS       | MongoDB        |
| ----------- | -------------- |
| Table       | Collection     |
| Row         | Document       |
| Column      | Field          |
| Primary Key | _id field      |
| Join        | Embedded, Link |
| Group by    | Aggregation    |
