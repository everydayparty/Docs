CentOS 7 개발서버 설치
======================

시작하며
--------
제로 베이스에서 개발 서버를 세팅할 일이 생겼다.
이전에는 일종에 템플릿화된 서버와 추가 설치 서버를 사용하여 초기 개발 세팅을 했었다.
그래서 아주 기초적인 부분에서 세팅은 삽질의 연속이 될 것으로 판단되어 문서를 남기기로 했다.
캡쳐이미지라던가 그런건  이미 지난일이라서 없다.

설치 디스크 만들기및 클린설치(Windows 10)
-----------------------------------------
- [사이트](https://www.centos.org/download/)에서 DVD ISO 다운로드
- [Win32DiskImager](https://sourceforge.net/projects/win32diskimager/)를 사용하여 굽기 : img파일이지만 그냥 구우면 되더라
- USB로 부팅
- GUI 서버로 설치 : 데스크 탑이 아직 안와서 겸용으로 쓰기 위해서 GUI도 같이 설치되도록 함
- 디스크는 그냥 파티션 전체 삭제하고 자동으로 설정되도록함
- 설치되면서 계정 추가화면이 나오는데 root와는 별도로 관리자 계정 생성

불필요 패키지 삭제
------------------
- LibreOffice 등 일부 패키지 삭제 : webcam 관련 패키지 삭제하다가 GUI 부팅이 안되어서 다시 설치함
- 사실 이건 불필요한 작업이니 그냥 패스하거나 취향대로

samba 설정
------------
### 패키지설치
    yum install samba samba-client samba-common samba-server nmb
### 유저및 폴더추가
    adduser user
    passwd user
    mkdir /home/dev/smbshare
    chown dev. /home/dev/smbshare
    chmod 777 /home/dev/smbshare
### /etc/samba/smb.conf 변경
    # [homes] 섹션 주석처리, printer 섹션 주석처리함
    # workgroup = WORKGROUP
    load printers = no
    # share 편집
    [share]
    comment = Default Folder
    path = /home/dev/smbshare
    public = no
    browsable = yes
    read only = no
    guest ok = no
    valid user = @dev, dev
    writable = yes
    write list = dev
    create mask = 0664
    directory mask = 0775
### 서비스 자동시작 설정
    systemctl enable smb
    systemctl enable nmb
### 파이어월 열기
    firewall-cmd --permanent --zone=public --add-service=samba

급 작성을 끝내며
------
이 이후에 nginx 설치, phusion passenger+redmine 설치 연동, git 관련설정, uwsgi를 이용한 django 개발환경 꾸미기 등을 하였으나..
docker관련된 내용 보면서 아.. 이건 삽질이었구나 깨닫게 되어서 이만 문서 작성을 중지할까 한다.
뭐 이건 사실 마크다운 연습겸해서 작성한거고
이후에 문서는 추가된다면 docker를 이용한 개발 환경설정이 될 것으로 보인다.

