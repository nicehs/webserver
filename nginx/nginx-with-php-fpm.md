---
title: nginx-with-php-fpm
tags: []
---

### Phần 3: Cài đặt php-fpm
Tìm kiếm gói php-fpm
```
yum search php-fpm
```
Cài đặt gói php-fpm
```
yum install -y php-fpm.x86_64
```
Cho phép nginx sử dụng php-fpm trong file /etc/php-fpm.d/www.conf
```
#Tại dòng 39 và 41
user = nginx
group = nginx
```
Tao file .php trong thư mục /home/drupal.linex.vn/index.php
```
<?php
        echo "this is drupal.linex.vn with php";
?>
```
Cấu hình nginx với file .php
```
ssl_password_file /var/lib/nginx/ssl_password.txt;
server {
        listen 443 ssl;
        server_name drupal.linex.vn;
        root /home/drupal.linex.vn;
        index index.php index.html;
        ssl_certificate         /etc/pki/tls/certs/drupal.linex.vn.crt;
        ssl_certificate_key     /etc/pki/tls/private/drupal.linex.vn.key;
        location ~ \.php$ {
                fastcgi_pass    127.0.0.1:9000;
                fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include         fastcgi_params;
        }
}
```
- **location ~ \.php$** có nghĩa là nếu thấy URI trỏ đến file đuôi .php thì thực thi các dòng lệnh bên trong. Để hiểu rõ hơn về location [xem tại đây](http://nginx.org/en/docs/beginners_guide.html)
- **fastcgi_pass**: kết nối với php-fpm thông qua cổng 9000 với địa chỉ 127.0.0.1
- **fasicgi_param**: thực thi script file 
- **include**: trỏ đến file fastgi_params.conf để webserver có thể hiểu các biến fastcgi  

Kiểm tra lại file cấu hình nginx và php-fpm
```
nginx -t
php-fpm -t
```
Restart lại nginx và start php-fpm
```
systemctl restart nginx
systemctl start php-fpm
```

Mở trình duyệt và test
![](image-kkplmr3x.png)
