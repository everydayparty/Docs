Ubuntu 20.04 LTS에 Wordpress 5.7.2 설치
======================================

시작하며
-------

워드프레스에 사이트 개발할 일이 발생했다.
뭐 PHP개발자이긴 하지만, 이런 예전 방식 코드는 안본지 너무 오래된데다가 워드프레스는 참 소스가 보기 어렵더라...
어쨌든 개발은 해야 하니 로컬 설치 부터 해보도록 하자.
일단 환경은 저사양 셀러론 머신에 신규 설치를 usb 만들어서 했다.
참고로 [공식 포럼 문서](https://wordpress.org/support/article/how-to-install-wordpress) 기반으로 설치하면 좋을 것 같다.

워드프레스 다운로드및 압축풀기
------------------------
```bash
wget https://wordpress.org/latest.tar.gz #다운받아서
tar -xzvf latest.tar.gz #압축풀고
rm -rf ./latest.tar.gz #삭제
```

MariaDB 설치하기
--------------
```bash
sudo apt install -y mariadb-server mariadb-client
sudo systemctl enable mariadb #부팅시에 알아서 시작하도록
sudo mysql #접속해서 잘 되는지 확인해보자
```

PHP 설치하기
----------
```bash
sudo apt update #필요하면 sudo apt upgrade 도 하자
sudo apt -y install php7.4-fpm php7.4-common php7.4-mysql php7.4-gmp php7.4-curl php7.4-intl php7.4-mbstring php7.4-xmlrpc php7.4-gd php7.4-xml php7.4-cli php7.4-zip zip unzip
sudo systemctl enable php7.4-fpm #부팅시 알아서 시작하도록
```
