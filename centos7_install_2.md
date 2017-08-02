CentOS 7.3 October CMS 설치
=========================

시작하며
--------
Docker를 사용하여 개발에 필요한 설정을 하려고 했다.
필요한게 October CMS라서 Docker허브에서 누군가가 만들어 놓은 이미지를 다운받았다.
로컬 볼륨도 설정 가능해서 잘 되는가 싶었는데, artisan 관련 오류도 나고 정상적으로 실행이 안되었다.
도커를 이용해서 개발환경을 꾸미는 목적이 뭔가 로컬 환경을 클린하게 유지하기 위함인데, 로컬에다가 php관련세팅을 한다는게 맞지 않아서 어떻게 할까 고민되었다.
게다가 로컬에 설치한다고 해도 정상 동작한다는 보장도 없다.
어차피 지금은 소스보기와 리서치 작업 정도만 할꺼라서 로컬 볼륨에 마운트 안하고 그냥 진행해도 되지만 일단 나중을 위해서 CentOS 기반으로 작업 관련 설치를 진행할까 한다.
compose를 사용하면 좋겠지만.. 일단 그거는 그거대로 연구가 많이 필요해서 일단 단순 CentOS:latest - 7.3 기반으로 설치를 진행할까 한다.
웹서버, PHP, Laravel, October CMS 순으로 설정예정이다.
그리고 가능하면 yum 이나 rpm 기반으로 순정설치하고 별도로 설치하는것은 피할 예정이다.

기초 시스템,PHP 관련 설치
------------
### repo 설치/PHP 7.1 설치

    yum install epel-release
    # 결과에서 아래의 오류 발생하면
    #Loaded plugins: fastestmirror, ovl
    #Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=container error was 14: curl#6 - "Could not resolve host: mirrorlist.centos.org; Unknown error"
    # /etc/resolv.conf 에서 nameserver 8.8.8.8 로 수정후 재실행
    yum update # 패키지 epel 업그레이드
    rpm -ivh http://rpms.remirepo.net/enterprise/remi-release-7.rpm
    yum --enablerepo=remi update remi-release
    yum update
    yum --enablerepo=remi-php71 install -y php php-common php-fpm php-mysql

### nginx 설치및 세팅
    yum install nginx
