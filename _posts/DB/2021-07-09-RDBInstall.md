---
layout: article
title: "[RocksDB] RocksDB 설치방법"
excerpt: RocksDB의 API활용을 위한 RocksDB 설치방법을 설명합니다.
categories:
  - 'RocksDB'
tags:
  - 'DB'
  - 'RocksDB'
last_modified_at: 2021-07-01T08:06:00-05:00
mode: immersive
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
toc: true
comment: true
cover: https://user-images.githubusercontent.com/28651727/124127962-4667a500-dab7-11eb-9474-3630dd90c915.png
---
<br>

<div align=center>
<div class="card">
  <div class="card__image">
    <img class="image" src="https://user-images.githubusercontent.com/28651727/124079851-a09b4280-da84-11eb-87c1-c8e525739628.png"/>
  </div>
</div>
</div>

깃허브의 `facebook/rocksdb` repo의 [install.md](https://github.com/facebook/rocksdb/blob/master/INSTALL.md)를 참고하였습니다.

## Dependecies
먼저 의존성 패키지를 설치합니다.

```
sudo apt-get install -y \
  libgflags-dev \
  libsnappy-dev \
  zlib1g-dev \
  libbz2-dev \
  libzstd-dev \
  liblz4-dev
```

## Install 
깃에서 RocksDB를 클론합니다.
```
git clone https://github.com/facebook/rocksdb.git
```
다음으로 make를 해주어야하는데 `make`나 `make all`은 디버그 모드로 컴파일하고, `make static_lib`는 rocksdb를 릴리즈 모드로 컴파일 합니다.  
RocksDB 깃허브의 설치 가이드에는 릴리즈 모드인 `make static_lib`로 설치하기를 권장하고 있습니다.

> **디버그 모드(Debug Mode) vs 릴리즈 모드(Release Mode)**
>  
> **디버그 모드**의 경우 실행파일에 디버깅 정보를 포함하여 실행파일의 상태정보를 확인 가능합니다. 다만 이러한 디버깅 정보를 포함하기 때문에 속도가 릴리즈 모드에 비해 다소 느립니다  
> 
> 반면 **릴리즈 모드**의 경우 이러한 디버깅 정보를 포함하지않기 때문에 속도가 디버그 모드에 비해 빠르고 파일의 크기가 비교적 작습니다. 

릴리즈모드로 컴파일합니다(db_bench도 같이 컴파일하고싶으면 뒤에 추가해줍니다)
```
cd rocksdb
make static_lib db_bench
```

컴파일이 완료되었으면 다음의 간단한 예제프로그램을 컴파일 해봅니다.

```c++
#include <assert.h>
#include "rocksdb/db.h"

rocksdb::DB* db;
rocksdb::Options options;
int main() {
    options.create_if_missing = true;
    rocksdb::Status status =
        rocksdb::DB::Open(options, "/tmp/testdb", &db);
    assert(status.ok());
}
```

g++ 컴파일 옵션에는 다음과 같이 rocksdb를 포함해 줍니다.
```
g++ test.cpp -lrocksdb -ldl -lpthread
```