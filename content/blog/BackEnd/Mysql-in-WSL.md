---
title: "[MySql] WSL에서 mysql사용환경 만들기"
date: 2021-03-02 16:21:13
category: 'BackEnd'
draft: false
---

## MySQL이란?

MySQL은 관계형 데이터베이스 관리 시스템(RDBMS: Relational DBMS)으로 오픈 소스이며, 다중 사용자와 다중 스레드를 지원합니다. C언어, C++, JAVA, PHP 등 여러 프로그래밍 언어를 위한 다양한 API를 제공하고 있습니다.

## MySQL 서버 및 클라이언트 설치

MySQL Server는 DBMS로 server를 이용하는 방법에는 Workbench를 이용하는 방법 Shell을 사용하는 방법 두가지가 있습니다.

### Step 1
다음 명령으로 mysql을 설치해 줍니다.
```
$sudo apt-get install mysql-server
```

설치가 완료되면 다음 명령으로 MySQL이 정상적으로 설치되었는지 확인할 수 있습니다.

```
$mysql --version
```

mysql서비스를 실행합니다. 
```
$service mysql start
```


### Step 2
다음 명령어를 실행하고 설정한 비밀번호를 입력하면 MySQL server에 연결할 수 있습니다. (비밀번호를 설정하지 않았다면 Enter누르면 됩니다)
```
sudo mysql -u root -p
```

### Step 3

현재 서버의 데이터베이스를 모두 보고 싶다면
```
mysql> show databases;
```

서버를 종료하고싶다면
```
mysql> exit
```

## Windows에 Workbench를 설치하여 연결하는 방법

### Step 1
>https://dev.mysql.com/downloads/file/?id=501541

위 주소에서 mysql-community를 다운로드 받아 Workbench를 설치합니다.

### Step 2
WSL쉘에서 mysql server를 실행합니다
```
sudo /etc/init.d/mysql start
```
WSL에서 서버를 실행하고 root는 remote접속이 제한되어있기 때문에 remote접속을 위한 새로운 유저를 만듭니다.
```
$ sudo mysql -u root -p
```
```
mysql> create user '<username>'@'%' identified by '<password>'
```
'username'@'%'에서 %는 remote접근을 허용합니다.  
username과 password는 사용자 임의로 정하면 됩니다.

이후 유저가 생성되었는지 확인하려면 다음명령을 통해 확인할 수 있습니다.
```
mysql> select user, host from user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| <username>       | %         |
| debian-sys-maint | localhost |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
```

### Step 3

앞서 설치한 workbench를 실행하고 +모양 버튼을 클릭해서 Setup New Connection창을 열어줍니다.

- HostName : 127.0.0.1
- Port : 3306
- Username : 앞서 생성한 유저이름
  
이후 testconnection을 클릭하면 정상적으로 연결된것을 확인 할 수 있습니다.