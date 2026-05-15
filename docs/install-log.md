## 1. Ubuntu 22.04 In Virtualbox


-- 
# 목차 
1. Ubuntu 22.04 Init Setup
2. Python 3.11 Install
3. Airflow 3.1 Install And Init Setup
   

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
- Database migrating done!

1. 관리자 계정 생성 아직 로그인할 수 있는 "열쇠"가 없습니다. 웹 UI에 접속하려면 계정을 먼저 파야 합니다.



# FAB(사용자 관리 모듈) 설치
pip install apache-airflow-providers-fab

1. PostgreSQL 설치 및 설정  -- 메타데이터를 postgres로 수정 
먼저 Ubuntu 시스템에 Postgres를 설치하고 Airflow가 사용할 전용 방을 만들어줘야 합니다.

Bash
# 1. Postgres 설치
sudo apt update
sudo apt install postgresql postgresql-contrib -y

# 2. Postgres 접속
sudo -u postgres psql     -- psql 창이 뜨면 아래 쿼리를 한 줄씩 입력하세요:

-- 1. 에어플로우용 유저 생성 (아이디: airflow / 비번: airflow)
CREATE USER airflow WITH PASSWORD 'airflow';

-- 2. 에어플로우용 DB 생성 ("LATIN1" ->  "UTF-8")
CREATE DATABASE airflow OWNER airflow ENCODING 'UTF8' LC_COLLATE 'C' LC_CTYPE 'C' TEMPLATE template0;

-- 3. 권한 부여 (CREATE ROLE)
GRANT ALL PRIVILEGES ON DATABASE airflow TO airflow;         --  airflow database airflow 유저한테 모든 권한(ALL PRIVILIGES)을 부여(GRANT)한다  
- GRANT

-- 4. Airflow 3용 소유권 이전
ALTER DATABASE airflow OWNER TO airflow;
- ALTER DATABASE
  
-- 5. [필수] public 스키마 권한 (Postgres 15+ 대응)
\c airflow      -- connect airflow database 
GRANT ALL ON SCHEMA public TO airflow;   -- Airflow User에게 public (postgres default 스키마 명) 마스터 권한 입무 : public 스키마도 명시적으로 허락받은 사람만 써!
- GRANT
- 
-- 6. 설정 최종 확인

\l
airflow=# \l             (airflow DB LATIN1 -> UTF 8)  
                             List of databases
   Name    |  Owner   | Encoding | Collate | Ctype |   Access privileges
-----------+----------+----------+---------+-------+-----------------------
 airflow   | airflow  | LATIN1   | en_US   | en_US | =Tc/airflow          +
           |          |          |         |       | airflow=CTc/airflow
 postgres  | postgres | LATIN1   | en_US   | en_US |
 template0 | postgres | LATIN1   | en_US   | en_US | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
 template1 | postgres | LATIN1   | en_US   | en_US | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
(4 rows)

airflow=# \l          (잘된거) 
                             List of databases
   Name    |  Owner   | Encoding | Collate | Ctype |   Access privileges
-----------+----------+----------+---------+-------+-----------------------
 airflow   | airflow  | UTF8     | C       | C     | =Tc/airflow          +
           |          |          |         |       | airflow=CTc/airflow
 postgres  | postgres | LATIN1   | en_US   | en_US |
 template0 | postgres | LATIN1   | en_US   | en_US | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
 template1 | postgres | LATIN1   | en_US   | en_US | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
(4 rows)




# Airflow DB가 "LATIN1" 이면 UTF-8로 바꿔줘야 한다 (한글이 깨지는 문제 발생) Ubuntu 22.04 언어 


airflow=# \du 
                                   List of roles
 Role name |                         Attributes                         | Member of
-----------+------------------------------------------------------------+-----------
 admin     |                                                            | {}
 airflow   |                                                            | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}

\q


pip install apache-airflow-providers-postgres psycopg2-binary

vim ~/airflow/airflow.cfg

(기존)sql_alchemy_conn = sqlite:////home/ubuntu/airflow/airflow.db 
(수정) sql_alchemy_conn = postgresql+psycopg2://airflow:airflow@localhost/airflow

(airflow_venv) ubuntu@ubuntu:~$ airflow db migrate
DB: postgresql+psycopg2://airflow:***@localhost/airflow
Performing upgrade to the metadata database postgresql+psycopg2://airflow:***@localhost/airflow


airflow config get-value core auth_manager
airflow.api_fastapi.auth.managers.simple.simple_auth_manager.SimpleAuthManager

Auth Manager 교체 가이드 (Simple → FAB)
이 절차대로 진행하시면 airflow fab 명령어가 마법처럼 나타날 겁니다.

① 설정 파일 수정 (airflow.cfg)
Bash
vim ~/airflow/airflow.cfg

#auth_manager = airflow.api_fastapi.auth.managers.simple.simple_auth_manager.SimpleAuthManager
auth_manager = airflow.providers.fab.auth_manager.fab_auth_manager.FabAuthManager

다시 확인
airflow config get-value core auth_manager
airflow.providers.fab.auth_manager.fab_auth_manager.FabAuthManager

-- 에어플로우가 내부적으로 실행한 쿼리 (의사 코드) airflow users create 에는 바로 아래 2줄을 입력한 샘이다
-- INSERT INTO ab_user (username, first_name, last_name, email, password, active)
-- VALUES ('admin', 'Admin', 'User', 'admin@example.com', 'pbkdf2:sha256:...', True);

airflow users create \
    --username admin \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@example.com \
    --password admin

- 에어플로우가 Postgres DB의 ab_user 테이블에 관리자 레코드를 성공적으로 박아넣었다는 확정
User "admin" created with role "Admin"


api-server , 스케쥴러 동시에 켜야 한다 

airflow web UI 접속 
 airflow api-server --port 8080
  ____________       _____________
 ____    |__( )_________  __/__  /________      __
____  /| |_  /__  ___/_  /_ __  /_  __ \_ | /| / /
___  ___ |  / _  /   _  __/ _  / / /_/ /_ |/ |/ /
 _/_/  |_/_/  /_/    /_/    /_/  \____/____/|__/
2026-05-15T05:39:48.719647Z [info     ] Running the uvicorn with:
Apps: all
Workers: 1
Host: 0.0.0.0:8080
Timeout: 120
Logfiles: -
================================================================= [airflow.cli.commands.api_server_command] loc=api_server_command.py:54
/home/ubuntu/airflow_venv/lib/python3.11/site-packages/airflow/api_fastapi/execution_api/routes/__init__.py:23 DeprecationWarning: 'HTTP_422_UNPROCESSABLE_ENTITY' is deprecated. Use 'HTTP_422_UNPROCESSABLE_CONTENT' instead.
2026-05-15T05:39:49.074712Z [warning  ] Could not import pandas. Holidays will not be considered. [airflow.example_dags.plugins.workday] loc=workday.py:41
/home/ubuntu/airflow_venv/lib/python3.11/site-packages/flask_limiter/extension.py:324 UserWarning: Using the in-memory storage for tracking rate limits as no storage was explicitly specified. This is not recommended for production use. See: https://flask-limiter.readthedocs.io#configuring-a-storage-backend for documentation about configuring the storage backend.
/home/ubuntu/airflow_venv/lib/python3.11/site-packages/airflow/api_fastapi/core_api/routes/public/__init__.py:36 DeprecationWarning: 'HTTP_422_UNPROCESSABLE_ENTITY' is deprecated. Use 'HTTP_422_UNPROCESSABLE_CONTENT' instead.
INFO:     Started server process [51990]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8080 (Press CTRL+C to quit)


두번쨰 세션 연결  
source ~/airflow_venv/bin/activate

airflow scheduler


(airflow_venv) ubuntu@ubuntu:~$ airflow scheduler
  ____________       _____________
 ____    |__( )_________  __/__  /________      __
____  /| |_  /__  ___/_  /_ __  /_  __ \_ | /| / /
___  ___ |  / _  /   _  __/ _  / / /_/ /_ |/ |/ /
 _/_/  |_/_/  /_/    /_/    /_/  \____/____/|__/
2026-05-15T05:43:50.135586Z [warning  ] Could not import pandas. Holidays will not be considered. [airflow.example_dags.plugins.workday] loc=workday.py:41
2026-05-15T05:43:50.169862Z [info     ] Starting log server on http://[::]:8793 [airflow.utils.serve_logs.core] loc=core.py:50
2026-05-15T05:43:50.269185Z [info     ] Starting the scheduler         [airflow.jobs.scheduler_job_runner.SchedulerJobRunner] loc=scheduler_job_runner.py:1054
2026-05-15T05:43:50.274819Z [info     ] Loaded executor: :LocalExecutor: [airflow.executors.executor_loader] loc=executor_loader.py:281
2026-05-15T05:43:50.281491Z [info     ] Adopting or resetting orphaned tasks for active dag runs [airflow.jobs.scheduler_job_runner.SchedulerJobRunner] loc=scheduler_job_runner.py:2276
WARNING:  ASGI app factory detected. Using it, but please consider setting the --factory flag explicitly.
INFO:     Started server process [52106]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://:8793 (Press CTRL+C to quit)

airflow 서버는 GuestOs인데 WebUI 접속은 HostOS (Win10) 이라 아래와 같이 포트포워딩 해줘야 한다  


Host OS 크롬에서 localhost:8080 접속
<img width="1370" height="225" alt="image" src="https://github.com/user-attachments/assets/3c335e16-59cc-42f9-83e0-1644098de61f" />


<img width="1911" height="704" alt="image" src="https://github.com/user-attachments/assets/ab18993c-d99e-4f40-a7fb-f2df71b5813e" />

ID : admin 
PASS: admin

<img width="1435" height="602" alt="image" src="https://github.com/user-attachments/assets/c04adfc1-c0bb-4939-a2ad-0b19c767c0e1" />

