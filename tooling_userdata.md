#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0b11afded1f7e6dff fs-094122548e6a2a4ec:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
git clone https://github.com/alagbaski/tooling.git
mkdir /var/www/html
sudo cp -rf tooling/html/*  /var/www/html/
cd tooling
mysql -h devtankdb.c3m0wu4iwvc5.us-west-1.rds.amazonaws.com -u timi -p Welber923007! toolingdb < tooling-db.sql
cd /home/ec2-user
sudo touch healthstatus
cd /var/www/html
sudo sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('devtankdb.c3m0wu4iwvc5.us-west-1.rds.amazonaws.com', 'timi', 'Welber923007!', 'toolingdb');/g" functions.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd
sudo systemctl status httpd
