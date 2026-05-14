## 1. Ubuntu 22.04 

- ubuntu incloude in sudo group  -- ubuntu 일반 유저도 sudo 명령어를 사용하기 위해서는 sudo group 에 ubuntu 계정을 넣어줘야한다  
  - su -
  - password 입력
  - root@ubuntu:#  sudo usermod -aG sudo ubuntu
  - su ubuntu
  - ubuntu@ubuntu:/root$ cd
  - ubuntu@ubuntu:~$  

- packege list Update & Upgrade
  - sudo apt update && sudo apt upgrade -y

- took install (Network check, Text editor, Distributed Shell
  - sudo apt-get install -y net-tools vim openssh-server pip

- python 3.11 install  
- 2. DeadSnakes PPA 저장소 추가 및 설치   -- Ubuntu 22.04에서 Python 3.11을 설치하려면 deadsnakes PPA 저장소를 사용하는 것이 가장 일반적이고 권장
  3. sudo add-apt-repository ppa:deadsnakes/ppa -y
  - sudo apt update
  - sudo apt install python3.11 python3.11-venv python3.11-dev -y
