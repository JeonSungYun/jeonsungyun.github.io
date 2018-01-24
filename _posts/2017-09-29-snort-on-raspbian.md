---
layout: post
title: "Snort on Raspberry Pi3"
comments: true
description: "Raspberry Pi3 (kernel 4.9.35-v7+)에 Snort 올리는 커맨드 히스토리"
keywords: "snort, raspberry pi, snort on rpi"
---

# 설치 환경
- Rpi Model : [Raspberry Pi3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)
- OS : [Raspbian Jessie](https://www.raspberrypi.org/blog/raspbian-jessie-is-here/), linux 4.9.35-v7+

# 설치 방법
## 사전 설치 요구 사항
``` sh
~$ sudo apt-get update && sudo apt-get upgrade
~$ sudo apt-get install -y build-essential libpcap-dev libpcre3-dev libdumbnet-dev bison flex
```

## 작업 디렉터리로 이동
``` sh
~$ mkdir ~/snort_src
~$ cd ~/snort_src
```
## DAQ 설치
``` sh
~/snort_src$ wget https://snort.org/downloads/snort/daq-2.0.6.tar.gz
~/snort_src$ tar -xvzf daq-2.0.6.tar.gz
~/snort_src$ cd daq-2.0.6
~/snort_src/daq-2.0.6$ ./configure
~/snort_src/daq-2.0.6$ make
~/snort_src/daq-2.0.6$ sudo make install
```

## Snort 설치
``` sh
~$ sudo apt-get install -y zlib1g-dev liblzma-dev openssl libssl-dev
~$ sudo apt-get install -y autoconf libtool pkg-config
~$ cd ~/snort_src
~/snort_src$ wget https://github.com/nghttp2/nghttp2/releases/download/v1.17.0/nghttp2-1.17.0.tar.gz
~/snort_src$ tar -xzvf nghttp2-1.17.0.tar.gz
~/snort_src$ cd nghttp2-1.17.0
~/snort_src/nghttp2-1.17.0$ autoreconf -i --force
~/snort_src/nghttp2-1.17.0$ automake
~/snort_src/nghttp2-1.17.0$ autoconf
~/snort_src/nghttp2-1.17.0$ ./configure --enable-lib-only
~/snort_src/nghttp2-1.17.0$ make
~/snort_src/nghttp2-1.17.0$ sudo make install
~/snort_src/nghttp2-1.17.0$ cd ~/snort_src
~/snort_src$ wget https://snort.org/downloads/snort/snort-2.9.9.0.tar.gz
~/snort_src$ tar -xvzf snort-2.9.9.0.tar.gz
~/snort_src$ cd snort-2.9.9.0
~/snort_src/snort-2.9.9.0$ ./configure --help
~/snort_src/snort-2.9.9.0$ ./configure --enable-sourcefire
~/snort_src/snort-2.9.9.0$ make
~/snort_src/snort-2.9.9.0$ sudo make install
~/snort_src/snort-2.9.9.0$ cd ~
~$ sudo ldconfig
~$ sudo ln -s /usr/local/bin/snort /usr/sbin/snort
~$ sudo apt-get install -y libnghttp2-dev
~$ snort -V
```

## Snort 를 NIDS 모드로 실행하도록 설정
``` sh
# Create the snort user and group:
~$ sudo groupadd snort
~$ sudo useradd snort -r -s /sbin/nologin -c SNORT_IDS -g snort
# Create the Snort directories:
~$ sudo mkdir /etc/snort
~$ sudo mkdir /etc/snort/rules
~$ sudo mkdir /etc/snort/rules/iplists
~$ sudo mkdir /etc/snort/preproc_rules
~$ sudo mkdir /usr/local/lib/snort_dynamicrules
~$ sudo mkdir /etc/snort/so_rules
# Create some files that stores rules and ip lists
~$ sudo touch /etc/snort/rules/iplists/black_list.rules
~$ sudo touch /etc/snort/rules/iplists/white_list.rules
~$ sudo touch /etc/snort/rules/local.rules
~$ sudo touch /etc/snort/sid-msg.map
# Create our logging directories:
~$ sudo mkdir /var/log/snort
~$ sudo mkdir /var/log/snort/archived_logs
# Adjust permissions:
~$ sudo chmod -R 5775 /etc/snort
~$ sudo chmod -R 5775 /var/log/snort
~$ sudo chmod -R 5775 /var/log/snort/archived_logs
~$ sudo chmod -R 5775 /etc/snort/so_rules
~$ sudo chmod -R 5775 /usr/local/lib/snort_dynamicrules
# Change Ownership on folders:
~$ sudo chown -R snort:snort /etc/snort
~$ sudo chown -R snort:snort /var/log/snort
~$ sudo chown -R snort:snort /usr/local/lib/snort_dynamicrules
~$ cd ~/snort_src/snort-2.9.9.0/etc/
~/snort_src/snort-2.9.9.0/etc$ sudo cp *.conf* /etc/snort
~/snort_src/snort-2.9.9.0/etc$ sudo cp *.map /etc/snort
~/snort_src/snort-2.9.9.0/etc$ sudo cp *.dtd /etc/snort
~/snort_src/snort-2.9.9.0/etc$ cd ~/snort_src/snort-2.9.9.0/src/dynamic-preprocessors/build/usr/local/lib/snort_dynamicpreprocessor/
~/snort_src/snort-2.9.9.0/src/dynamic-preprocessors/build/usr/local/lib/snort_dynamicpreprocessor$ sudo cp * /usr/local/lib/snort_dynamicpreprocessor/
~/snort_src/snort-2.9.9.0/src/dynamic-preprocessors/build/usr/local/lib/snort_dynamicpreprocessor$ cd ~
# We will use PulledPork to manage our rulesets, which combines all the rules into a single file. The following line will comment out all rulesets in our snort.conf
~$ sudo sed -i "s/include \$RULE\_PATH/#include \$RULE\_PATH/" /etc/snort/snort.conf
~$ sudo vi /etc/snort/snort.conf
.. ~
.. var RULE_PATH /etc/snort/rules
.. var SO_RULE_PATH /etc/snort/so_rules
.. var PREPROC_RULE_PATH /etc/snort/preproc_rules
.. var WHITE_LIST_PATH /etc/snort/rules/iplists
.. var BLACK_LIST_PATH /etc/snort/rules/iplists
.. ~
.. include $RULE_PATH/local.rules
~$ sudo snort -T -i eth0 -c /etc/snort/snort.conf
~$ 
```

## Barnyard2 설치
``` sh
~$ sudo apt-get install -y mysql-server libmysqlclient-dev mysql-client autoconf libtool
~$ sudo vi /etc/snort/snort.conf
.. ~
.. # output unified2: filename merged.log, limit 128, nostamp, mpls event types, vlan event types}
.. output unified2: filename snort.u2, limit 128
~$ cd ~/snort_src
~/snort_src$ wget https://github.com/firnsy/barnyard2/archive/master.tar.gz -O barnyard2-Master.tar.gz
~/snort_src$ tar zxvf barnyard2-Master.tar.gz
~/snort_src$ cd barnyard2-master
~/snort_src/barnyard2-master$ autoreconf -fvi -I ./m4
~/snort_src/barnyard2-master$ cd ~
~$ sudo ln -s /usr/include/dumbnet.h /usr/include/dnet.h
~$ sudo ldconfig
~$ ./configure --with-mysql --with-mysql-libraries=/usr/lib/arm-linux-gnueabihf
~$ make
~$ sudo make install
~$ /usr/local/bin/barnyard2 -V
~$ sudo cp ~/snort_src/barnyard2-master/etc/barnyard2.conf /etc/snort/
# the /var/log/barnyard2 folder is never used or referenced
# but barnyard2 will error without it existing
~$ sudo mkdir /var/log/barnyard2
~$ sudo chown snort.snort /var/log/barnyard2
~$ sudo touch /var/log/snort/barnyard2.waldo
~$ sudo chown snort.snort /var/log/snort/barnyard2.waldo
~$ mysql -u root -p
    mysql> create database snort;
    mysql> use snort;
    mysql> source ~/snort_src/barnyard2-master/schemas/create_mysql
    mysql> CREATE USER 'snort'@'localhost' IDENTIFIED BY 'snort';
    mysql> grant create, insert, select, delete, update on snort.* to 'snort'@'localhost';
    mysql> exit
~$ sudo vi /etc/snort/barnyard2.conf
.. ~
.. output database: log, mysql, user=snort password=snort dbname=snort host=localhost sensor name=sensor01
~$ sudo chmod o-r /etc/snort/barnyard2.conf
~$ sudo barnyard2 -c /etc/snort/barnyard2.conf -d /var/log/snort -f snort.u2 -w /var/log/snort/barnyard2.waldo -g snort -u snort
```

## PulledPork 설치
```sh
~$ cd ~/snort_src
~/snort_src$ wget https://github.com/shirkdog/pulledpork/archive/master.tar.gz -O pulledpork-master.tar.gz
~/snort_src$ tar xzvf pulledpork-master.tar.gz
~/snort_src$ cd pulledpork-master/
~/snort_src/pulledpork-master$ sudo cp pulledpork.pl /usr/local/bin
~/snort_src/pulledpork-master$ sudo chmod +x /usr/local/bin/pulledpork.pl
~/snort_src/pulledpork-master$ sudo cp etc/*.conf /etc/snort
~/snort_src/pulledpork-master$ /usr/local/bin/pulledpork.pl -V

# snort.org 회원가입 후 마이페이지에서 oinkcode 획득 
# for example, 644307aff7158349e726e048fb31cbf9e748a449

sudo vi /etc/snort/pulledpork.conf
Line 19: enter your oinkcode where appropriate (or comment out if no oinkcode)
Line 29: Un-comment for Emerging threats ruleset (not tested with this guide)
Line 74: change to: rule_path=/etc/snort/rules/snort.rules
Line 89: change to: local_rules=/etc/snort/rules/local.rules
Line 92: change to: sid_msg=/etc/snort/sid-msg.map
Line 96: change to: sid_msg_version=2
Line 119: change to: config_path=/etc/snort/snort.conf
Line 133: change to: distro=Ubuntu-12-04
Line 141: change to: black_list=/etc/snort/rules/iplists/black_list.rules
Line 150: change to: IPRVersion=/etc/snort/rules/iplists
sudo /usr/local/bin/pulledpork.pl -c /etc/snort/pulledpork.conf -l

sudo vi /etc/snort/snort.conf
include $RULE_PATH/snort.rules

sudo snort -T -c /etc/snort/snort.conf -i eth0

sudo crontab -e
01 04 * * * /usr/local/bin/pulledpork.pl -c /etc/snort/pulledpork.conf -l
```

sudo vi /lib/systemd/system/snort.service
[Unit]
Description=Snort NIDS Daemon
After=syslog.target network.target
[Service]
Type=simple
ExecStart=/usr/local/bin/snort -q -u snort -g snort -c /etc/snort/snort.conf -i ens160
[Install]
WantedBy=multi-user.target

sudo systemctl enable snort
sudo systemctl start snort
systemctl status snort

sudo vi /lib/systemd/system/barnyard2.service
[Unit]
Description=Barnyard2 Daemon
After=syslog.target network.target
[Service]
Type=simple
ExecStart=/usr/local/bin/barnyard2 -c /etc/snort/barnyard2.conf -d /var/log/snort -f snort.u2 -q -w /var/log/snort/barnyard2.waldo -g snort -u snort -D -a /var/log/snort/archived_logs
[Install]
WantedBy=multi-user.target

sudo systemctl enable barnyard2
sudo systemctl start barnyard2
systemctl status barnyard2

## BASE 설치
sudo apt-get install -y apache2 libapache2-mod-php5 php5 php5-mysql php5-common php5-gd php5-cli php-pear
sudo pear install -f --alldeps Image_Graph
cd ~/snort_src
wget https://sourceforge.net/projects/adodb/files/adodb-php5-only/adodb-520-for-php5/adodb-5.20.8.tar.gz
tar -xvzf adodb-5.20.8.tar.gz
sudo mv adodb5 /var/adodb
sudo chmod -R 755 /var/adodb
wget https://sourceforge.net/projects/secureideas/files/BASE/base-1.4.5/base-1.4.5.tar.gz
tar xzvf base-1.4.5.tar.gz
sudo mv base-1.4.5 /var/www/html/base/
cd /var/www/html/base
sudo cp base_conf.php.dist base_conf.php
sudo vi /var/www/html/base/base_conf.php

$BASE_urlpath = '/base'; # line 50
$DBlib_path = '/var/adodb/'; #line 80
$alert_dbname = 'snort'; # line 102
$alert_host = 'localhost';
$alert_port = '';
$alert_user = 'snort';
$alert_password = 'MySqlSNORTpassword'; # line 106

//$graph_font_name = "Verdana";
//$graph_font_name = "DejaVuSans";
//$graph_font_name = "Image_Graph_Font";
$graph_font_name = "";

sudo chown -R www-data:www-data /var/www/html/base
sudo chmod o-r /var/www/html/base/base_conf.php

sudo service apache2 restart

## Performance Profiling

profile profile rule, preproc
- configure 다시 해서 Snort 를 다시 빌드해야함.
- snort.conf 에서 세 군데 주석 해제 해야함

이것도 /var/snort -> /var/log/snort 로 바꾸기
preprocessor perfmonitor: time 300 file /var/log/snort/snort.stats pktcnt 10000

(사실 이러면 conf 파일에 로그 디렉터리 경로들 다 때려넣는게 좋을듯;;)

흠.. 근데 이게 잘 안된다.

## 메일 알림 설정
sudo pear install --alldeps mail
sudo pear install --alldeps Mail_Mime

It's now

pear upgrade --force --alldeps http://pear.php.net/get/PEAR-1.10.1
Then upgrade the rest

pear clear-cache
pear update-channels
pear upgrade
pear upgrade-all

sudo pear install Net_SMTP


## Trouble Shooting
sudo rm /var/log/snort/*

sudo systemctl start snort
(snort 서비스 로딩 완료 후. snort.u2.nnnnn... 이 생성되기까지 기다려줘야함.. )
sudo systemctl start barnyard2

이렇게 하고 오랜 시간을 기다려주니 barnyard2 에서 mysql 로 데이터를 쌓기 시작함.

barnyard clean
~$ mysql -u root -p
    mysql> drop database snort;
    mysql> create database snort;
    mysql> use snort;
    mysql> source ~/snort_src/barnyard2-master/schemas/create_mysql
    mysql> grant create, insert, select, delete, update on snort.* to 'snort'@'localhost';
    mysql> exit


./configure --help 를 보니
--enable-sourcefire 만 제공함.

java installer 가 download 시에 에러 난다.
Run the below commands on terminal,

sudo dpkg -P oracle-java7-installer
sudo apt-get -f install

[unified2 file read]
u2spewfoo <file>