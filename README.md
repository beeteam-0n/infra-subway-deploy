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
  - KEY-hahoho87.pem

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 :
  - | 서브넷 | IP대역 | az |
    | --- | --- | --- |
    | hahoho87-public-subnet-a | 192.168.22.0/26 | az-2a |
    | hahoho87-public-subnet-b | 192.168.22.64/26 | az-2c |
    | hahoho87-internal-subnet-a | 192.179.22.128/27 | az-2a |
    | hahoho87-admin-subnet-c | 192.168.22.160/27 | az-2c |

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요
- URL : http://www.hahoho87-subway.kro.kr/ (http://15.165.92.73)

---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : https://hahoho87-subway.kro.kr

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.
```shell
#!/bin/bash
## 변수 설정
txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray

EXECUTION_PATH=$(pwd)
SHELL_SCRIPT_PATH=$(dirname $0)
BRANCH=$1
PROFILE=$2
JAR_NAME="subway-0.0.1-SNAPSHOT.jar"

## compare with remote repository
function check_df() {
  git fetch
  master=$(git rev-parse $BRANCH)
  remote=$(git rev-parse origin/$BRANCH)
  if [[ $master == $remote ]]; then
    echo -e "${txtred}>> [$(date)] Repository is already up to date.${txtrst}"
    exit 0
  fi
}

## pull origin repository
function pull() {
  echo -e ""
  echo -e "${txtgrn}>> [$(date)] UPDATE SOURCE CODE"
  echo -e "${txtgrn}>> [$(date)] cdm : git pull origin ${BRANCH}${txtst}"
  git pull origin ${BRANCH}
}

## build application
function build() {
  echo -e ""
  echo -e "${txtgrn}>> [$(date)] GRADLE BUILD. ${txtrst}"
  read -p ">> [$(date)] build with tests? (y)es | (n)o. " -n 1 -r
  echo -e ""
  if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo -e "${txtgrn}>> [$(date)] cmd : ./gradlew clean build${txtrst}"
    ./gradlew clean build
  elif [[ $REPLY =~ ^[Nn]$ ]]; then
    echo -e "${txtgrn}>> [$(date)] cmd : ./gradlew clean build -x test${txtrst}"
    ./gradlew clean build -x test
  else
    build
  fi
}

## terminate existing process
function terminate() {
  echo -e ""
  echo -e "${txtgrn}>> [$(date)] TERMINATE EXISTING PROCESS ${txtrst}"
  PID=$(pgrep -f ${JAR_NAME})
  if [ -z "${PID}" ]; then
    echo -e "${txtred}>> [$(date)] Not fond running ${JAR_NAME} application. ${txtrst}"
  else
    sig_term
    ck_terminate
  fi
}

## check terminate
function ck_terminate() {
  sleep 3
  if [ -z "${PID}" ]; then
    sleep 3
    print_not_term
    if [ -z "${PID}" ]; then
      sleep 3
      print_not_term
      if [ -z "${PID}" ]; then
        sig_kill
        echo -e "${txtred}>> [$(date)] ${JAR_NAME} is forced terminated. ${txtrst}"
      else
        print_term
      fi
    else
      print_term
    fi
  else
    print_term
  fi
}

## sig_term
function sig_term() {
  kill -15 ${PID}
}

## sig_kill
function sig_kill() {
  kill -9 ${PID}
}

## not terminated message
function print_not_term() {
  echo -e "${txtred}>> [$(date)] ${JAR_NAME} is not terminate yet. ${txtrst}"
}

## terminated message
function print_term() {
  echo -e "${txtgrn}>> [$(date)] ${JAR_NAME} is terminated. ${txtst}"
}

## run application
function run() {
  echo -e ""
  echo -e "${txtgrn}>> [$(date)] DEPLOY APPLICATION ${txtst}"
  nohup java -jar -Dserver.port=8080 -Dspring.profiles.active=$PROFILE $EXECUTION_PATH/build/libs/$JAR_NAME 1>infra-subway-deploy-log 2>&1 &
  PID=$(pgrep -f ${JAR_NAME})
  echo -e "${txtgrn}>> [$(date)] (env: ${PROFILE}) APPLICATION IS STARTED  |  PID : ${PID}"
}

## deploy
function deploy() {
  check_df
  pull
  build
  terminate
  run
}

echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  <<<<< SCRIPT >>>>>${txtrst}"
if [[ $# -ne 2 ]]; then
  echo -e ""
  echo -e "${txtylw} $1 Enter the branch name of the repository :${txtred}$(git remote show origin | grep 'Fetch URL')"
  echo -e "${txtylw} $2 Enter the environment profile to deploy :${txtred}{ prod | test }"
  echo -e "${txtylw}=======================================${txtrst}"
  check_df
  exit
else
  echo -e ""
  echo -e "${txtgrn} [Branch] : $BRANCH ${txtrst}"
  echo -e "${txtgrn} [Profile] : $PROFILE ${txtrst}"
fi
deploy
echo -e "${txtylw}=======================================${txtrst}"


```


