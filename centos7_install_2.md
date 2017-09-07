CentOS 7.3 October CMS 설치
=========================

시작하며
--------
Docker를 사용하여 개발에 필요한 설정을 하려고 했다.  
필요한게 October CMS라서 Docker허브에서 누군가가 만들어 놓은 이미지를 다운받았다.  
로컬 볼륨도 설정 가능해서 잘 되는가 싶었는데, artisan 관련 오류도 나고 정상적으로 실행이 안되었다.  
도커를 이용해서 개발환경을 꾸미는 목적이 뭔가 로컬 환경을 클린하게 유지하기 위함인데, 로컬에다가 php관련세팅을 한다는게 맞지 않아서 어떻게 할까 고민되었다.  
게다가 로컬에 설치한다고 해도 windows용 PHP가 정상 동작한다는 보장도 없다.  
어차피 지금은 소스보기와 리서치 작업 정도만 할꺼라서 로컬 볼륨에 마운트 안하고 그냥 진행해도 되지만 일단 나중을 위해서 CentOS 기반으로 작업 관련 설치를 진행할까 한다.  
처음엔 도커 이미지 기반으로 설치하려고 했으나 시스템상 맞지 않는 부분이 있어서 계획을 변경하여 리눅스 가상머신에 centos 설치하고 진행하기로 했다.  
웹서버, PHP, Laravel, MySQL, October CMS 등을 설치예정이다.  
root계정으로 설치함을 가정.  
가능하면 yum 이나 rpm 기반으로 순정설치를 원칙으로 하고 있다.  

기초 시스템,PHP 관련 설치
------------
### repo 설치

    yum install epel-release
    # 결과에서 아래의 오류 발생하면
    #Loaded plugins: fastestmirror, ovl
    #Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=container error was 14: curl#6 - "Could not resolve host: mirrorlist.centos.org; Unknown error"
    # /etc/resolv.conf 에서 nameserver 8.8.8.8 로 수정후 재실행
    yum -y update # 패키지 epel 업그레이드
    rpm -ivh http://rpms.remirepo.net/enterprise/remi-release-7.rpm
    yum --enablerepo=remi update remi-release
    vi /etc/selinux/config
    #SELINUX=disabled 개발편의를 위해서 변경한다.
    systemctl disable firewalld # 개발편의를 위해서 방화벽을 끈다.
    reboot

### PHP 7.1 설치
    yum --enablerepo=remi-php71 install -y php php-common php-fpm php-mbstring php-xml php-mcrypt php-zip php-mysql php-gd
    systemctl start php-fpm
    systemctl enable php-fpm
    # systemctl status php-fpm 확인
    cp /etc/php.ini /etc/php.ini.orig
    vi /etc/php.ini # 보안관련 아래 값으로 수정해준다.
    #cgi.fix_pathinfo = 0
    #allow_url_fopen = On # 보안에는 off가 좋지만 일단 composer위해 on
    #expose_php = Off
    #display_errors = On # 개발머신이라서 on
    cp /etc/php-fpm.d/www.conf /etc/php-fpm.d/www.conf.orig
    vi /etc/php-fpm.d/www.conf # 아래와 같이 수정한다
    #user = nginx
    #group = nginx
    #listen.owner = nginx
    #listen.group = nginx
    #listen.mode = 0660

### nginx 설치및 세팅
yum 으로 nginx 사용해서 패키지 설치한다.  
/etc/nginx/conf.d/cms.conf에 아래 내용을 추가한다.  

    server {
        listen       80;
        server_name  cms.*;
        root         /var/www/html;
        index        index.php index.html index.htm;

        include /etc/nginx/default.d/*.conf;
    }

/etc/nginx/default.d/php.conf에 아래 내용을 추가한다.  

    location ~ \.php$ {
        if (!-f $request_filename) {
            return 404;
        }
        fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;
        fastcgi_intercept_errors    on;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root/$fastcgi_script_name;
        include        fastcgi_params;
    }

/var/www/html/index.php 파일에 php코드를 넣어서 테스트 해본다.  

### MariaDB 설치

    yum -y install mariadb-server mariadb
    systemctl start mariadb
    mysql_secure_installation # 적당한 값으로.. pw는 없이

아래 항목의 값들로 /etc/my.cnf.d/ 아래 파일들을 수정한다.  
systemctl restart mariadb로 재시작  
cli 접속 show variables like 'c%'; 명령으로 확인

    [mysqld]
    port=3306
    default_storage_engine=innodb
    init_connect="SET collation_connection = utf8_general_ci"  
    init_connect="SET NAMES utf8"  
    character-set-server = utf8
    collation-server = utf8_general_ci

    [client]
    port=3306
    default-character-set = utf8

    [mysqldump]
    default-character-set = utf8

    [mysql]
    default-character-set = utf8

### October CMS 설치/설정
    cd /var/www/html
    curl -s https://octobercms.com/api/installer | php
    php artisan october:install # 이후에 입력하는 값들은 적당히
