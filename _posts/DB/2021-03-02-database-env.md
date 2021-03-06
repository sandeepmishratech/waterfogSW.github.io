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

### Step 1
```
$sudo apt-get install mysql-server mysql-client
```

설치가 완료되면 다음 명령으로 MySQL이 정상적으로 설치되었는지 확인할 수 있습니다.

### Step 2
```
$mysql --version
```

### Step 3
보안 설정을 위해 다음 명령을 실행합니다.
```
sudo mysql_secure_installation
```
>여기서 Can't connect to local MySQL server through socket '/var/>run/mysqld/mysqld.sock' 와 같은 에러가 발생한다면 다음 명령어를 실행합니다.
>```
>sudo service mysql restart
>```

옵션과 비밀번호를 설정해 줍니다. 
- Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
- Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
- Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
- Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y

### Step 4
다음 명령어를 실행하고 앞서 설정한 비밀번호를 입력하면 MySQL server에 연결할 수 있습니다.
```
sudo mysql -u root -p
```

### Step 5

현재 서버의 데이터베이스를 모두 보고 싶다면
```
mysql> show databases;
```

서버를 종료하고싶다면
```
mysql> exit
```