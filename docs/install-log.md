## 1. Ubuntu 22.04 In Virtualbox

- ubuntu incloude in sudo group  -- 최초 로그인 시 ubuntu 일반 유저도 sudo 명령어를 사용하기 위해서는 sudo group 에 ubuntu 계정을 넣어줘야한다  
  - su -        -- root 계정 로그인 (password 입력 프롬프트 뒤에 "$" -> "#" 확인)   
  - root@ubuntu:#  sudo usermod -aG sudo ubuntu   -- ubuntu 일반 계정에 sudo Group 에 포함시켜 sudo 사용권한을 가진다  
  - su ubuntu           -- root 계정에서 ubuntu 일반 계정으로 전환 (마찬가지로 프롬프트 뒤에 "#" -> "$" 확인) 
  - ubuntu@ubuntu:/root$ cd        -- ubuntu 홈 디렉토리 
  - ubuntu@ubuntu:~$  

- packege list Update & Upgrade
  - sudo apt update && sudo apt upgrade -y

- took install (Network check, Text editor, Distributed Shell
  - sudo apt install -y net-tools vim openssh-server pip

- python 3.11 install  
  - DeadSnakes PPA 저장소 추가 및 설치   -- (default Python 3.10) Python 3.11을 설치하려면 deadsnakes PPA 저장소를 사용하는 것이 가장 일반적이고 권장
    - sudo add-apt-repository ppa:deadsnakes/ppa -y
    - sudo apt update
    - sudo apt install python3.11 python3.11-venv python3.11-dev -y
