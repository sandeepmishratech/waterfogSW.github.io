---
title: "[DB]Linux에서 mysql사용환경 만들기"
excerpt: Linux에서 mysql사용환경 만들기
categories:
  - 'DB'
tags:
  - 'Database'
  - 'MySQL'
last_modified_at: 2021-03-02T08:06:00-05:00
toc: true
---

## MySQL이란?

MySQL은 관계형 데이터베이스 관리 시스템(RDBMS: Relational DBMS)으로 오픈 소스이며, 다중 사용자와 다중 스레드를 지원합니다. C언어, C++, JAVA, PHP 등 여러 프로그래밍 언어를 위한 다양한 API를 제공하고 있습니다.

## MySQL 서버 및 클라이언트 설치

```terminal
$sudo apt-get install mysql-server mysql-client
```

설치가 완료되면 다음 명령으로 MySQL이 정상적으로 설치되었는지 확인할 수 있습니다.

```terminal
$mysql --version
```

다음 명령어로 MySQL을 실행하고 종료할 수 있습니다.
```
sudo /tec/init.d/mysql start
```

데이터 베이스에 접속하기 위해서는 다음과 같은 명령어로 접속합니다. root부분에는 다른 사용자 명이 들어갈수 있고, 초기비밀번호를 따로 설정하지 않으면 비밀번호 입력창은 enter를 눌러 넘깁니다.

```
$mysql -u root -p
```
