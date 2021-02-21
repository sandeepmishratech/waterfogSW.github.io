---
title: Colab VScode에 연결해서 사용하기
excerpt: Connect colab ssh to vscode
categories:
  - Colab
tags:
  - 'Colab'
  - 'SSH'
  - 'VScode'
last_modified_at: {}
published: true
---
##Colab VScode에 연결해서 사용하기

![ex_screenshot](https://www.stechstar.com/user/zbxe/files/attach/images/3151/119/064/c9342957dc10efc2948e86e7718ae516.png)

VScode에 Colab을 연결시키기 위해서는 먼저 Colab에 다음코드를 복사해서 실행한다. 

'''
import random, string
password = ''.join(random.choice(string.ascii_letters + string.digits) for i in range(20))

#Download ngrok
! wget -q -c -nc https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
! unzip -qq -n ngrok-stable-linux-amd64.zip
#Setup sshd
! apt-get install -qq -o=Dpkg::Use-Pty=0 openssh-server pwgen > /dev/null
#Set root password
! echo root:$password | chpasswd
! mkdir -p /var/run/sshd
! echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
! echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config
! echo "LD_LIBRARY_PATH=/usr/lib64-nvidia" >> /root/.bashrc
! echo "export LD_LIBRARY_PATH" >> /root/.bashrc

#Run sshd
get_ipython().system_raw('/usr/sbin/sshd -D &')

#Ask token
print("Copy authtoken from https://dashboard.ngrok.com/auth")
import getpass
authtoken = getpass.getpass()

#Create tunnel
get_ipython().system_raw('./ngrok authtoken $authtoken && ./ngrok tcp 22 &')
#Print root password
print("Root password: {}".format(password))
#Get public address
! curl -s http://localhost:4040/api/tunnels | python3 -c \
    "import sys, json; print(json.load(sys.stdin)['tunnels'][0]['public_url'])"
'''

Copy authtoken from https://dashboard.ngrok.com/auth
실행 하게 되면 위와 같은 주소가 출력되는데 위 주소에 들어가서 구글 계정과 연결하고 Authtoken을 복사하여 입력한다. 

Root password 와 tcp 주소가 출력되면 성공이다.

이를통해 VScode의 Remote-ssh를 통해 연결하면 된다.
tcp : 0.tcp.ngrok.io -p <portNum>


