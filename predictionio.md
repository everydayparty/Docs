Prediction.IO 설치
=================

-1. 준비에 앞서
---------------

이거 안하는게 좋을듯? 프로젝트 취소되었단다. 사실 나도 이거 문서 왜 만들었는지 모르는거라..

0.준비
-----

- ubuntu Server 16.04.3 LTS


1.JAVA 설치
----------

설치시에 ssh서버만 선택하고 설치하니 java가 없다.
패키지 관리자로 설치한다

    sudo su - #그냥 명령어 하기 편해서 root로 진행함
    apt install -y default-jre openjdk-8-jdk-headless

결과

    java -version
    openjdk version "1.8.0_131"
    OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2ubuntu1.16.04.3-b11)
    OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
    javac -version
    javac 1.8.0_131

JAVA_HOME 변수도 설정

    # 경로 확인
    /bin/readlink -f $(which javac)
    # 결과 : /usr/lib/jvm/java-8-openjdk-amd64/bin/javac
    # 아래 명령으로 profile파일에 추가
    echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> /etc/profile

1.Prediction.IO 및 서드파티 프로그램 다운로드
--------------------------------------

    #mkdir ./predictionio;cd ./predictionio #미리 압축풀 폴더를 만들어 놓는다.
    wget http://apache.mirror.cdnetworks.com/incubator/predictionio/0.12.0-incubating/apache-predictionio-0.12.0-incubating.tar.gz
    tar zxvf apache-predictionio-0.12.0-incubating.tar.gz
    mkdir vendors; cd vendors
    #스파크
    wget http://d3kbcqa49mib13.cloudfront.net/spark-2.1.1-bin-hadoop2.6.tgz
    tar zxvf spark-2.1.1-bin-hadoop2.6.tgz
    #엘라스틱서치
    wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.2.tar.gz
    tar zxvf elasticsearch-5.5.2.tar.gz
    #HBASE
    wget http://apache.mirror.cdnetworks.com/hbase/1.2.6/hbase-1.2.6-bin.tar.gz
    tar zxvf hbase-1.2.6-bin.tar.gz

2.환경설정 및 구동
--------------

    cd ../conf
    cp pio-env.sh.template pio-env.sh

아래 항목을 PGSQL 에서 각각의 솔루션으로 변경

    PIO_STORAGE_REPOSITORIES_METADATA_SOURCE=ELASTICSEARCH
    PIO_STORAGE_REPOSITORIES_EVENTDATA_SOURCE=HBASE
    PIO_STORAGE_REPOSITORIES_MODELDATA_SOURCE=LOCALFS

다음 항목을 주석

    # PIO_STORAGE_SOURCES_PGSQL_PASSWORD accordingly
    # PIO_STORAGE_SOURCES_PGSQL_TYPE=jdbc
    # PIO_STORAGE_SOURCES_PGSQL_URL=jdbc:postgresql://localhost/pio
    # PIO_STORAGE_SOURCES_PGSQL_USERNAME=pio
    # PIO_STORAGE_SOURCES_PGSQL_PASSWORD=pio

다음 항목을 주석을 풀거나 변경

    PIO_STORAGE_SOURCES_ELASTICSEARCH_TYPE=elasticsearch
    PIO_STORAGE_SOURCES_ELASTICSEARCH_HOSTS=localhost
    PIO_STORAGE_SOURCES_ELASTICSEARCH_PORTS=9200
    PIO_STORAGE_SOURCES_ELASTICSEARCH_SCHEMES=http
    PIO_STORAGE_SOURCES_ELASTICSEARCH_HOME=$PIO_HOME/vendors/elasticsearch-5.5.2

    PIO_STORAGE_SOURCES_LOCALFS_TYPE=localfs
    PIO_STORAGE_SOURCES_LOCALFS_PATH=$PIO_FS_BASEDIR/models

    PIO_STORAGE_SOURCES_HBASE_TYPE=hbase
    PIO_STORAGE_SOURCES_HBASE_HOME=$PIO_HOME/vendors/hbase-1.2.6
