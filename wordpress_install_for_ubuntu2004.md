Ubuntu 20.04 LTS에 Wordpress 5.7.2 설치
======================================

# 시작하며

워드프레스에 사이트 개발할 일이 발생했다.
뭐 PHP개발자이긴 하지만, 이런 예전 방식 코드는 안본지 너무 오래된데다가 워드프레스는 참 소스가 보기 어렵더라...
어쨌든 개발은 해야 하니 로컬 설치 부터 해보도록 하자.
일단 환경은 저사양 셀러론 머신에 신규 설치를 usb 만들어서 했다.
참고로 [공식 포럼 문서](https://wordpress.org/support/article/how-to-install-wordpress) 기반으로 설치하면 좋을 것 같다.

# 사용 모듈 설치하기

## MariaDB 설치하기

```bash
sudo apt install -y mariadb-server mariadb-client
sudo systemctl enable mariadb #부팅시에 알아서 시작하도록
sudo mysql #접속해서 잘 되는지 확인해보자
```

### DB 및 유저 생성

```sql
CREATE database `wpdb`;
GRANT ALL PRIVILEGES on wpdb.* to `wpuser` identified by '****'
FLUSH privileges; # 권한 적용
USE wpdb;
SELECT @@character_set_database, @@collation_database; # 
# 확인하면 아래와 같은 결과가 나올것이다.
# +--------------------------+----------------------+
# | @@character_set_database | @@collation_database |
# +--------------------------+----------------------+
# | utf8mb4                  | utf8mb4_general_ci   |
# +--------------------------+----------------------+
# 1 row in set (0.000 sec)
```

## nginx 설치하기

```bash
sudo apt install -y nginx
sudo systemctl enable nginx
```

## PHP 설치하기

```bash
sudo apt update #필요하면 sudo apt upgrade 도 하자
sudo apt -y install php7.4-fpm php7.4-common php7.4-mysql php7.4-gmp php7.4-curl php7.4-intl php7.4-mbstring php7.4-xmlrpc php7.4-gd php7.4-xml php7.4-cli php7.4-zip zip unzip
sudo systemctl enable php7.4-fpm
```

## 워드프레스 다운로드및 압축풀기

```bash
cd /var/www/
sudo wget https://wordpress.org/latest.tar.gz #다운받아서
sudo tar -xzvf latest.tar.gz #압축풀고
sudo rm -rf ./latest.tar.gz #삭제
sudo chown -R www-data. ./wordpress # 파일소유 권한 변경하고
sudo chmod -R 755 ./wordpress # 폴더 접근 권한도 수정하고

```

## 서버 설정 작성및 변경

/etc/hosts 파일에 다음 한줄 추가한다.
```
127.0.1.1       wpdev.local www.wpdev.local
```

/etc/nginx/sites-available/wordpress 작성
```
server {
    listen 80;
    server_name wpdev.local.
    root /var/www/wordpress;
    index index.php ;
    location ~ \.(gif|jpg|png)$ {
        add_header Vary "Accept-Encoding";
        add_header Cache-Control "public, no-transform, max-age=31536000";
    }
    location ~* \.(css|js)$ {
        add_header Cache-Control "public, max-age=604800";
        log_not_found off;
        access_log off;
    }
    location ~*.(mp4|ogg|ogv|svg|svgz|eot|otf|woff|woff2|ttf|rss|atom|ico|zip|tgz|gz|rar|bz2|doc|xls|exe|ppt|tar|mid|midi|wav|bmp|rtf|cur)$ {
        add_header Cache-Control "max-age=31536000";
        access_log off;
    }
    charset utf-8;
    server_tokens off;
    location / {
        try_files $uri $uri/ =404;
    }
    location ~ /\.ht {
        deny all;
    }
    location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
    }
}

```
사이트를 활성화 해준다.
```bash
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
sudo unlink /etc/nginx/site-enabled/default # 기본 사이트 비활성화
sudo systemctl restart nginx # nginx재시작
```

## 사이트 설정
http://wpdev.local 사이트에 접속해보면 설정화면으로 들어갈 수 있다.
여기서 아까 생성한 DB와 유저계정 등을 입력하고 설정하면 된다.

# 마치며
뭐 사실 간단하게 적긴 했는데.. 나도 그냥 구글링하면서 검색하면서 쓴거라 뭐 실제로 하면서 트러블 슈팅같은게 필요하거나 환경이 약간달라서 안되는게 있을 수도 있다.
