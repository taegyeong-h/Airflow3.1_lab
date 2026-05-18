# 01. Ubuntu 26.04 기본 설정 및 파이썬 환경 구축

이 문서는 Airflow 3.1을 설치하기 전, 우분투 시스템 패키지를 최신화하고 필수 의존성 및 파이썬 가상환경을 구축하는 단계입니다.

## 💻 테스트 환경 (Environment)
- **OS**: Ubuntu 26.04 LTS (Noble Numbat)
- **Python**: v3.12.3 (Ubuntu 기본 제공 버전)
- **Airflow**: v3.2.0

---

## 1. 시스템 패키지 업데이트 및 업그레이드
우분투의 패키지 저장소 리스트를 갱신하고, 설치된 패키지들을 최신 버전으로 업그레이드합니다.

```bash
$ sudo apt update && sudo apt upgrade -y
```

## 2. OS 버전 및 파이썬 버전 확인
업데이트가 완료된 후, 시스템 버전과 파이썬 기본 버전이 테스트 환경 스펙과 일치하는지 확인합니다.

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       noble

$ python3 --version
Python 3.12.3
```

## 시스템 패키지 업데이트 및 업그레이드
```bash
$ sudo apt update && sudo apt upgrade -y
$ sudo apt install -y net-tools vim openssh-server pip
```
