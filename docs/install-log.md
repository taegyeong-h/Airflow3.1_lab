## 1. Ubuntu 22.04 In Virtualbox

- ubuntu incloude in sudo group  -- 최초 로그인 시 ubuntu 일반 유저도 sudo 명령어를 사용하기 위해서는 sudo group 에 ubuntu 계정을 넣어줘야한다  
  - su -        -- root 계정 로그인 (password 입력 프롬프트 뒤에 "$" -> "#" 확인)   
  - root@ubuntu:#  sudo usermod -aG sudo ubuntu   -- ubuntu 일반 계정에 sudo Group 에 포함시켜 sudo 사용권한을 가진다  
  - su ubuntu           -- root 계정에서 ubuntu 일반 계정으로 전환 (마찬가지로 프롬프트 뒤에 "#" -> "$" 확인) 
  - ubuntu@ubuntu:/root$ cd        
  - ubuntu@ubuntu:~$              -- ubuntu 홈 디렉토리 에서 작업 시작

- packege list Update & Upgrade
  - sudo apt update && sudo apt upgrade -y

- took install (Network check, Text editor, Distributed Shell
  - sudo apt install -y net-tools vim openssh-server pip

- python 3.11 install  
  - DeadSnakes PPA 저장소 추가 및 설치   -- (default Python 3.10) Python 3.11을 설치하려면 deadsnakes PPA 저장소를 사용하는 것이 가장 일반적이고 권장
    - sudo add-apt-repository ppa:deadsnakes/ppa -y
    - sudo apt update
    - sudo apt install python3.11 python3.11-venv python3.11-dev -y
    - python 3.11 --version
      - python 3.11.15 -- 3.11.15 버전 확인 

- airflow_venv'라는 이름의 3.11 전용 방(폴더) 만들기 -- 위에서도 말했지만 Ubuntu 22.04 default python 3.10 이라 3.11 전용 방을 만들어아 햠 
  - python3.11 -m venv ~/airflow_venv

- 그 방으로 들어가기 (활성화)
  - source ~/airflow_venv/bin/activate
  - (airflow_venv) ubuntu@ubuntu:~$
  - pip install --upgrade pip
  - pip 24.0 -> 26.1.1
  - export AIRFLOW_HOME=~/airflow    -- "너의 모든 설정 파일, 로그, 데이터베이스 파일은 이 폴더(~/airflow)에 저장하고 관리해!"라고 '집 주소'를 알려주는 것
  - (airflow_venv) ubuntu@ubuntu:~$ echo "export AIRFLOW_HOME=~/airflow" >> ~/.bashrc
  - tail -1 ~/.bashrc               -- bashrc 편집기 마지막 줄 확인
  - export AIRFLOW_HOME=~/airflow
  - echo $AIRFLOW_HOME   -> 출력값 (/home/ubuntu/airflow)


  Airflow 3.1.2 Install
 
[👉 Apache Airflow GitHub 바로가기](https://github.com/apache/airflow)  
화면 왼쪽 상단에 main 이라고 써진 버튼이 보일 거예요. 그걸 클릭하면 메뉴가 뜹니다.

거기에 3.1.2을 입력하거나 목록에서 constraints-3.1.2을 찾아서 클릭하세요
<img width="607" height="534" alt="20260514_170337" src="https://github.com/user-attachments/assets/9f4f4b57-a662-4f17-94eb-964c09ed4caf" />

파이썬 버전 3.11 클릭
<img width="1303" height="1135" alt="image" src="https://github.com/user-attachments/assets/82b9a8b6-bed9-4aac-9037-7453409da1b1" />

 - 1. 설치할 에어플로우 버전과 파이썬 버전 지정
       - export AIRFLOW_VERSION=3.1.2
       - export PYTHON_VERSION=3.11

  - 2. 에어플로우 공식 '보증된 버전 목록' 주소 생성
       - export CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"

  - 3. 드디어 진짜 설치 명령어 실행! (인터넷 속도에 따라 2~5분 정도 소요됩니다)
      - pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
      - (airflow_venv) ubuntu@ubuntu:~$ airflow version -> 출력값(3.1.2)

-- Airflow가 데이터를 저장할 메타데이터(SQLite)를 생성
airflow db migrate
DB: sqlite:////home/ubuntu/airflow/airflow.db
Performing upgrade to the metadata database sqlite:////home/ubuntu/airflow/airflow.db
2026-05-14T08:14:07.739879Z [info     ] Context impl SQLiteImpl.       [alembic.runtime.migration] loc=migration.py:211
2026-05-14T08:14:07.740027Z [info     ] Will assume non-transactional DDL. [alembic.runtime.migration] loc=migration.py:214
2026-05-14T08:14:07.740619Z [info     ] Context impl SQLiteImpl.       [alembic.runtime.migration] loc=migration.py:211
2026-05-14T08:14:07.740708Z [info     ] Will assume non-transactional DDL. [alembic.runtime.migration] loc=migration.py:214
2026-05-14T08:14:07.740920Z [info     ] Creating Airflow database tables from the ORM [airflow.utils.db] loc=db.py:684
2026-05-14T08:14:07.741051Z [info     ] Creating context               [airflow.utils.db] loc=db.py:689
2026-05-14T08:14:07.741304Z [info     ] Binding engine                 [airflow.utils.db] loc=db.py:691
2026-05-14T08:14:07.741376Z [info     ] Pool status: NullPool          [airflow.utils.db] loc=db.py:693
2026-05-14T08:14:07.741434Z [info     ] Creating metadata              [airflow.utils.db] loc=db.py:695
2026-05-14T08:14:08.295474Z [info     ] Getting alembic config         [airflow.utils.db] loc=db.py:698
2026-05-14T08:14:08.297104Z [info     ] Stamping migration head        [airflow.utils.db] loc=db.py:705
2026-05-14T08:14:08.300581Z [info     ] Context impl SQLiteImpl.       [alembic.runtime.migration] loc=migration.py:211
2026-05-14T08:14:08.300780Z [info     ] Will assume non-transactional DDL. [alembic.runtime.migration] loc=migration.py:214
2026-05-14T08:14:08.341922Z [info     ] Running stamp_revision  -> cc92b33c6709 [alembic.runtime.migration] loc=migration.py:622
2026-05-14T08:14:08.348196Z [info     ] Airflow database tables created [airflow.utils.db] loc=db.py:708
Database migrating done!

1. 관리자 계정 생성 아직 로그인할 수 있는 "열쇠"가 없습니다. 웹 UI에 접속하려면 계정을 먼저 파야 합니다.

Airflow 2.0 Version
airflow users create \
    --username admin \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@example.com \
    --password admin

Airflow 3.0 Version

# FAB(사용자 관리 모듈) 설치
pip install apache-airflow-providers-fab





1. PostgreSQL 설치 및 설정  -- 메타데이터를 postgres로 수정 
먼저 Ubuntu 시스템에 Postgres를 설치하고 Airflow가 사용할 전용 방을 만들어줘야 합니다.

Bash
# 1. Postgres 설치
sudo apt update
sudo apt install postgresql postgresql-contrib -y

# 2. Postgres 접속
sudo -u postgres psql
psql 창이 뜨면 아래 쿼리를 한 줄씩 입력하세요:

SQL
-- 데이터베이스 생성
CREATE DATABASE airflow_db;

-- 사용자 생성 및 비밀번호 설정 (원하는 걸로 바꾸셔도 됩니다)
CREATE USER airflow_user WITH PASSWORD 'airflow_pass';

-- 권한 부여
GRANT ALL PRIVILEGES ON DATABASE airflow_db TO airflow_user;

-- 나가기
\q


