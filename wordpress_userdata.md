#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0fc9dab3a72101117 fs-03997d7f4e6450f54:/ /var/www/
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo yum module reset php -y
sudo yum module enable php:remi-7.4 -y
sudo yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
mkdir /var/www/html/
sudo cp -R /wordpress/* /var/www/html/
sudo cd /var/www/html/
touch healthstatus
sed -i "s/localhost/devtankdb.c3m0wu4iwvc5.us-west-1.rds.amazonaws.com/g" wp-config.php
sed -i "s/username_here/ruth/g" wp-config.php
sed -i "s/password_here/Welber923007!/g" wp-config.php
sed -i "s/database_name_here/devtankdb/g" wp-config.php
sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd
