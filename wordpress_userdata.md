#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0b9d75dd64b9c710f fs-0995dccc6c868a0d6:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
mkdir /var/www/html/
sudo cp -R /wordpress/* /var/www/html/
cd /var/www/html/
sudo touch healthstatus
sed -i "s/localhost/devtank-db.cz6ksuqwosgs.us-east-1.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/mark/g" wp-config.php 
sed -i "s/password_here/Forget00!/g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd
sudo systemctl status httpd
