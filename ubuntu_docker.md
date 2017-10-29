우분투 최신 버전 도커 설치 - 작성중
====================

1. ubuntu server 17xx 최신 버전 이미지로 설치
------------

2. 기존 구버전 도커 지우기
------------
    sudo apt remove docker docker-engine docker.io

3. 키와 저장소 설치
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo apt-key fingerprit 0EBFCD88
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable
    sudo apt update
    sudo apt install docker-ce
