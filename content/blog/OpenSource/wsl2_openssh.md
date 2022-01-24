---
title:  "[WSL] open-ssh설정(WSL 내부아이피 포트포워딩)"
date: 2021-08-10 16:21:13
category: 'OpenSource'
draft: false
---

몇달전만 하더라도 wsl환경에서 openssh를 실행하면 별다른 포트포워딩 없이 외부에서 접속할 수 있었던것 같았는데..

오늘 해보려니 갑자기 안되었다.

![image](https://user-images.githubusercontent.com/28651727/128743742-5707dfe9-1575-438a-b182-61f35f401a95.png)

wsl콘솔에서 ifconfig명령어를 실행하였을때 `172.22.78.179`라는 내부 아이피 주소를 사용하는것을 확인할 수 있었습니다.

저는 iptime공유기를 사용하므로 `192.168.0.~`에 해당하는 주소가 나올거라 생각했는데 그렇지 않았습니다. 

그래서 wsl내부 아이피를 로컬의 내부 아이피로 포트포워딩 해야 할것 같았고 다음과 같은 명령어를 통해 wsl 내부아이피의 포트포워딩을 진행하였습니다.

powershell(관리자권한)
```
netsh interface portproxy add v4tov4 listenport=22 listenaddress='0.0.0.0' connectport=22 connectaddress=172.22.78.179
```

깃허브에서 해당 이슈에 대한 내용을 확인할 수 있었습니다.
[Link](https://github.com/microsoft/WSL/issues/4150#issuecomment-504209723)

코멘트 중에 어떤분은 매번 파워쉘에서 스크립트를 입력하는 번거로움을 덜기위해 다음과 같은 스크립트를 코멘트로 달아주셨습니다.

```
$remoteport = bash.exe -c "ifconfig eth0 | grep 'inet '"
$found = $remoteport -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';

if( $found ){
  $remoteport = $matches[0];
} else{
  echo "The Script Exited, the ip address of WSL 2 cannot be found";
  exit;
}

#[Ports]

#All the ports you want to forward separated by coma
$ports=@(22);


#[Static ip]
#You can change the addr to your ip config to listen to a specific address
$addr='0.0.0.0';
$ports_a = $ports -join ",";


#Remove Firewall Exception Rules
iex "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";

#adding Exception Rules for inbound and outbound Rules
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP";
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP";

for( $i = 0; $i -lt $ports.length; $i++ ){
  $port = $ports[$i];
  iex "netsh interface portproxy delete v4tov4 listenport=$port listenaddress=$addr";
  iex "netsh interface portproxy add v4tov4 listenport=$port listenaddress=$addr connectport=$port connectaddress=$remoteport";
}
```

만약 Execution Policy오류가 발생하면
```
PowerShell.exe -ExecutionPolicy Bypass -File .\wsl_port.ps1
```
을 통해 해결할 수 있습니다.