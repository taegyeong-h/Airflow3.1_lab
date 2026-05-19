<img width="1048" height="702" alt="image" src="https://github.com/user-attachments/assets/c9dc9b24-e64d-46ba-b0a7-5d84e21e772e" /># 02. Airflow Standalone 구동 및 웹 UI 확인

이 단계에서는 설치 완료된 Airflow를 원클릭 `standalone` 모드로 구동하고, 웹 브라우저를 통해 관리자 화면에 접속하는 과정을 다룹니다.

---

## 1. Airflow Standalone 실행 전 기본 설정 
가상환경(`airflow_venv`)이 켜진 상태에서 아래 명령어를 실행하여 에어플로우의 모든 프로세스(웹서버, 스케줄러, SQLite DB)를 한 방에 켭니다.

```bash
# 가상환경 활성화 (안 켜져 있다면 실행)
source ~/airflow_venv/bin/activate
vim ~/airflow/airflow.cfg
```
```
[core]
# 1. 기존 설정 (예제 DAG 로드 활성화)
#load_examples = True

# 변경 후 설정 (예제 DAG 로드 차단)
load_examples = False

# 2. 스케줄러 실행 기준 시간대를 한국(KST)으로 변경
#default_timezone = utc
default_timezone = Asia/Seoul

[api]
host = 0.0.0.0  

port = 8080

# cpu 단일 코어 하나만 사용하겠다
workers = 1 


```






# 에어플로우 standalone 구동
airflow standalone
```

## 2. Airflow WebUI 실행 및 init 패스워드 확인
```bash
# host OS 브라우저에서 [airflow server]:8080 검색

# 에어플로우가 자동으로 생성한 무작위 문자열  
cat ~/airflow/simple_auth_manager_passwords.json.generated
{"admin": "whv4uKffU5hSPVGH"}

# 관리자 패스워드 수정 (서버 안내려도 바로 적용됌)
vim ~/airflow/simple_auth_manager_passwords.json.generated
{"admin": "admin"}
```
<img width="462" height="595" alt="image" src="https://github.com/user-attachments/assets/44dc0320-2245-496a-8659-ba01e4117416" />



