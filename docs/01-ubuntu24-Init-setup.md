## 💻 테스트 환경 (Environment)
- **OS**: Ubuntu 24.04.4 LTS (Noble Numbat)
- **Python**: v3.12.3 (Ubuntu 기본 제공 버전)
- **Airflow**: v3.1.0

## OS 버전 및 파이썬 버전 확인
현재 설치된 Ubuntu 버전과 기본 Python 버전을 확인합니다.

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.4 LTS
Release:        24.04
Codename:       noble

$ python3 --version
Python 3.12.3
```

## 시스템 패키지 업데이트 및 업그레이드
```bash
$ sudo apt update && sudo apt upgrade -y
```
