# 1. Ubuntu 26.04 In Virtualbox

## 설치 환경
### Host OS: Win 10
### Guest OS: Ubuntu 26.04
### Python: V3.11
### Airflow: 3.2

-- 
## 목차 
### Ubuntu 26.04 초기 설정 및 Python 3.11 가상환경 구축
### Airflow 3.2.1 Install And Init Setup
<br>
<br>

## Ubuntu 26.04 초기 설정 및 Python 3.11 가상환경 구축

### 1) Root 유저로 전환 후 ubuntu 유저를 sudo 그룹에 추가
```bash
su -
usermod -aG sudo ubuntu
exit
```

### 2) 일반 유저(ubuntu) 상태에서 패키지 업데이트
```
bash
# 최신 패키지 업데이트 및 업그레이드
sudo apt update && sudo apt upgrade -y

# 필수 도구(네트워크 체크, 텍스트 에디터, SSH, pip) 설치
sudo apt install -y net-tools vim openssh-server pip

# python 3.11 install
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.11 python3.11-venv python3.11-dev -y
python3.11 --version
# 출력 결과: Python 3.11.15
```

### 3) Airflow 전용 가상환경(venv) 생성 및 셋업
```
bash
# Ubuntu 26.04의 기본 파이썬(3.14)과 분리하기 위해 독립된 가상환경 방을 만듭니다.
python3.2 -m venv ~/airflow_venv
source ~/airflow_venv/bin/activate
# 정상 활성화 시 터미널 프롬프트 앞에 (airflow_venv)가 표시됩니다.

# 1. 터미널이 켜질 때마다 자동으로 경로를 잡도록 설정 파일 맨 아래에 주입 (한 줄로 끝)
echo "export AIRFLOW_HOME=~/airflow" >> ~/.bashrc

# 2. 변경된 설정 파일을 현재 터미널 창에 바로 새로고침(적용)
source ~/.bashrc

# 3. 정상적으로 경로가 잡혔는지 확인 (출력 결과: /home/ubuntu/airflow)
echo $AIRFLOW_HOME
# 출력 결과: /home/ubuntu/airflow

```

## Airflow 3.2.1 Install And Init Setup
 
[👉 Apache Airflow GitHub 바로가기](https://github.com/apache/airflow)  


화면 왼쪽 상단에 main 이라고 써진 버튼이 보일 거예요. 그걸 클릭하면 메뉴가 뜹니다.

거기에 3.2을 입력하거나 목록에서 constraints-3.2을 찾아서 클릭
<img width="736" height="698" alt="image" width="50%" src="https://github.com/user-attachments/assets/9762e6a0-caf5-4fef-9f8f-e1a34f39ef37" />

파이썬 버전 3.11 
<img width="1429" height="1354" alt="image" width="50%" src="https://github.com/user-attachments/assets/08756421-06aa-44e2-b197-ff47b7e083a1" />

```bash
# 1. 3.2.1 전용으로 고정된 제약조건 URL 주소로 변경
export CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-3.2.1/constraints-3.11.txt"

# 2. 설치 명령어 재실행
pip install "apache-airflow==3.2.1" --constraint "${CONSTRAINT_URL}"
airflow version
# 출력결과: 3.2.1
```









## 아래는 02. 03 파일로 구분 예정
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>





-- Airflow가 데이터를 저장할 메타데이터(SQLite)를 생성
airflow db migrate
- Database migrating done!


1. 관리자 계정 생성 아직 로그인할 수 있는 "열쇠"가 없습니다. 웹 UI에 접속하려면 계정을 먼저 파야 합니다.

## 2. Airflow 3.2 설치 및 Standalone 실행 (빠른 배포용)
(현재 진행 중인 standalone + SQLite 관련 내용 쭉 작성...)

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

1. JWT 발행자 설정 (500 에러 직접 해결)
아까 보셨던 [api] 섹션에 이 한 줄을 타이핑해서 넣어주세요.

[api]
auth_jwt_issuer = airflow  -- 추가 
2. 세션 암호화 키 설정 (로그인 튕김 방지)
[core] 섹션에서 secret_key를 찾아 아무 긴 문자열이나 넣어주세요. (자바의 세션 암호화 키와 같습니다.)

[core]

# 1. 기존에 있던 것
dags_folder = /home/ubuntu/airflow/dags

# 2. [추가] "암호화 키"
secret_key = your-super-secret-key-at-least-32-chars



(airflow_venv) ubuntu@ubuntu:~$ airflow db migrate
DB: postgresql+psycopg2://airflow:***@localhost/airflow
Performing upgrade to the metadata database postgresql+psycopg2://airflow:***@localhost/airflow



airflow config get-value api auth_jwt_issuer

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
airflow

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
 airflow api-server --host 0.0.0.0 --port 8080      -- airflow 2.0 "hostname" , airflow 3.0 "host"
 
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

