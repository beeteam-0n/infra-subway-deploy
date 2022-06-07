<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 README.md 파일에 작성하여 PR을 보내주세요.

### 0단계 - pem 키 생성하기

1. 서버에 접속을 위한 pem키를 [구글드라이브](https://drive.google.com/drive/folders/1dZiCUwNeH1LMglp8dyTqqsL1b2yBnzd1?usp=sharing)에 업로드해주세요

2. 업로드한 pem키는 무엇인가요.
    - key-kbh0581.pem

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 : 
   * VPC : 192.168.101.0/24
     * 외부망 :  
       - 192.168.101.0/26 (kbh0581-public-subnet-2a)
       - 192.168.101.64/26 (kbh0581-public-subnet-2c)
    - 내부망 :
      - 192.168.101.128/27 (kbh0581-private-subnet-2a) 
    - 관리용 
      - 192.168.101.160/27 (kbh0581-admin-subnet-2c) 
   

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요

- URL : http://www.kbh0581.kro.kr/



---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : 

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.



---
## 1단계 요구 사항 정리

### 망 구성

- [X] VPC 생성 : (192.168.101.0/24) - kbh0581-vpc
   
   - CIDR은 C class(x.x.x.x/24)로 생성. 이 때, 다른 사람과 겹치지 않게 생성
- [X] Subnet 생성
    - [X] 외부망으로 사용할 Subnet : 64개씩 2개 (AZ를 다르게 구성)
      - kbh0581-public-subnet-2a : 192.168.101.0/26 (ap_northeast-2a)
      - kbh0581-public-subnet-2c : 192.168.101.64/26 (ap_northeast-2c)
      
    - [X] 내부망으로 사용할 Subnet : 32개씩 1개
      - kbh0581-private-subnet-2a : 192.168.101.128/27 (ap_northeast-2a) 
    - [X] 관리용으로 사용할 Subnet : 32개씩 1개
      - kbh0581-admin-subnet-2c : 192.168.101.160/27 (ap_northeast-2c)
- [X] Internet Gateway 연결
  - kbh0581-igw
- [X] Route Table 생성
- [X] Security Group 설정
  - [X] 외부망 : kbh0581-sg-public
    - 전체 대역 : 80 포트 오픈 (8080포트 제거)
    - 관리망 : 22번 포트 오픈 
  - [X] 내부망 
    - 외부망 : 3306 포트 오픈
    - 관리망 : 22번 포트 오픈 
  - [X] 관리망
    - 자신의 공인 IP : 22번 포트 오픈
- [X] 서버 생성
    - [X] 외부망에 웹 서비스용도의 EC2 생성
      - kbh0581-ec2-webservice
    - [X] 내부망에 데이터베이스용도의 EC2 생성
      - kbh0581-ec2-database
    - [X] 관리망에 베스쳔 서버용도의 EC2 생성
      - kbh0581-ec2-bastion 
    - [X] 베스쳔 서버에 Session Timeout 600s 설정
    - [X] 베스쳔 서버에 Command 감사로그 설정

### 주의사항

- 다른 사람이 생성한 리소스는 손대지 말아요 🙏🏻
- 모든 리소스는 태그를 작성합니다. 이 때 자신의 계정을 Prefix로 붙입니다. (예: brainbackdoor-public)

### 웹 애플리케이션 배포

- [X] 외부망에 웹어플리케이션 배포
- [X] DNS 설정

--- 
### 2단계 서비스 배포하기 

- [X] 운영 환경 구성하기
  - [X] 웹 애플리케이션 앞단에 Reverse Proxy 구성하기
  - [X] 외부망에 Nginx로 Reverse Proxy를 구성
  - [X] Reverse Proxy에 TLS 설정
  - [X] 운영 데이터베이스 구성하기

- [ ] 개발 환경 구성하기
  - 설정 파일 나누기
    JUnit(test) : h2, 
    Local : docker(mysql)
    Prod : 운영 DB를 사용하도록 설정
