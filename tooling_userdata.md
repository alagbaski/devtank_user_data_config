#!/bin/bash
exec > /var/log/tooling.log 2>&1
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0f6b8862b94125b20 fs-0abadbc7b32b96fd0:/ /var/www/
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo yum module reset php -y
sudo yum module enable php:remi-7.4 -y
sudo yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
git clone https://github.com/alagbaski/tooling.git
mkdir /var/www/html
sudo cp -R /tooling/html/*  /var/www/html/
cd /tooling
mysql -h devtank-db.c3m0wu4iwvc5.us-west-1.rds.amazonaws.com -u ruth -p toolingdb < tooling-db.sql
cd /var/www/html/
touch healthstatus
sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('devtank-db.c3m0wu4iwvc5.us-west-1.rds.amazonaws.com', 'ruth', 'Welber923007!', 'toolingdb');/g" functions.php
sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup
sudo systemctl restart httpd
