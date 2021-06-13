Ubuntu 20.04 LTS에 Code Server 최신버전 설치
======================================

# 시작하며

서버 세팅한김에 code-server도 설치해서 개발서버의 능력을 극대화 해보기로 했다. 설치시 참고문서는 [개발자git설치문서](https://github.com/cdr/code-server/blob/main/docs/install.md) 기준으로 한다.

# 사용 모듈 설치하기

## code-server 다운로드

다운받기 위해서 먼저 curl을 설치해준다. 이미 설치되어 있다면 패스. 최신버전이 21.6.13일 기준 3.10.2 이므로 이걸 서치하도록 한다.

```bash
sudo apt install -y curl
curl -fOL https://github.com/cdr/code-server/releases/download/v3.10.2/code-server_3.10.2_amd64.deb
sudo dpkg -i code-server_3.10.2_amd64.deb
sudo systemctl enable --now code-server@$USER
```

## 서버 설정

~/.config/code-server/config.yaml 을 열어서 설정값을 자신에 맞도록 수정해준다.

```
bind-addr: 0.0.0.0:8080
auth: password
password: ****
cert: false
```
## 사용하기

localhost:8080이나 지금 local로 설정한 사이트주소 :8080해도 들어가진다.(hosts파일에 추가했으므로) 아까 환경설정에서 설정한 비번으로 접속잘되는지 확인하고 추가로 plugin 
