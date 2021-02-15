---
title: apache-with-drupal
tags: []
---

### Part 4: Apache with drupal
[Cấu hình php](https://github.com/nicehs/webserver/blob/main/apache/apache-with-php.md)

[Cài đặt database (MySQL)](https://github.com/nicehs/webserver/blob/main/database-mariadb.md)  

Tạo database cho drupal và kiểm tra
```
create database apadrupal;

show databases;
```
Tạo user và phân quyền
```
create user 'apache'@'localhost' identified by 'abc@123';

grant all privileges on apadrupal.* to 'apache'@'localhost' identified by 'abc@123';
```
Kiểm tra lại user và phân quyền
```
select user from mysql.user;

show grants for 'apache'@'localhost';
```
Download drupal https://ftp-origin.drupal.org/files/projects/drupal-x.tar.gz (x là phiên bản) và giải nén
```
wget --no-check-certificate https://ftp-origin.drupal.org/files/projects/drupal-7.9.tar.gz

tar -xzvf drupal-7.9.tar.gz
```
Chỉnh sửa trong file cấu hình
```
...
<VirtualHost test.linux.vn:443>
        DocumentRoot /home/test.linux.vn/drupal-7.9
        DirectoryIndex index.php index.html
        ServerName test.linux.vn
        ...
        <FilesMatch \.php$>
                SetHandler application/x-httpd-php
        </FilesMatch>
        ...
</VirtualHost>
```
Truy cập vào https://test.linux.vn để cấu hình drupal
Cài đặt các phần còn thiếu trong hình
![](image-kl6060fh.png)
- Download php-dom, php-gd, php-pdo
- Tạo và trao quyền cho thư mục sites/default/files và site/default/settings.php

Nhập các thông số để kết nối database
![](image-kl622pfy.png)
Tạo tài khoản quản trị
![](image-kl627vgb.png)
![](image-kl628lx3.png)
Finish và đăng nhập vào trang quản trị
![](image-kl62b8pk.png)
Sửa các tham số sau đây để có thể upload file kích thước lớn hơn 2M
- **upload_max_filesize** trong /etc/php.ini
- File cấu hình virtualhost thêm dòng **client_max_body_size 50M;**
