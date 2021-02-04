---
title: nginx-with-drupal
tags: []
---

### Phần 5: Cài đặt Drupal
Đầu tiên, kiểm tra phiên bản php hoặc php-fpm
```
php-fpm -v
```
> PHP 5.4.16 (fpm-fcgi) (built: Apr  1 2020 04:09:12)  
Copyright (c) 1997-2013 The PHP Group  
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies

Tuỳ vào phiên bản PHP, Nginx và MariaDB sẽ có những [requirement](https://www.drupal.org/docs/7/system-requirements/php-requirements) cho bộ source Drupal phù hợp. Ở ví dụ này ta sẽ cài bản Drupal 7.78. Download bộ source code tại https://ftp.drupal.org/files/projects và giải nén
Sửa thư mục root trỏ đến thư mục chứa source Drupal  
```
server {
    ...
    root /home/drupal.linex.vn/drupal-7.10;
    ...
}
```
Kiểm tra lại và khởi động lại dịch vụ
```
nginx -t
systemctl restart nginx
```
Mở trình duyệt và truy cập vào đường dẫn https://drupal.linex.vn  
Phân quyền cho thư mục /home/drupal.linex.vn/drupal-7.10
```
chown -R root:nginx /home/drupal.linex.vn/drupal-7.10
chmod -R 750 /home/drupal.linex.vn/drupal-7.10
```
![](image-kkpnjzdm.png)  
Nhấn tiếp tục cho đến phần Verify Requirement
![](image-kkpoyafj.png)  
- **PHP extension** cài thêm các gói php-dom php-gd php-pdo và thêm 1 dòng sau vào file /etc/php.ini để enable extension
```
extension=pdo.so
```
- **File system** tạo thư mục sites/default/files và phân quyền
```
cd /home/drupal.linex.vn/drupal-7.10
mkdir ./sites/default/files
chown -R root:nginx ./sites/default/files
chmod -R 770 ./sites/default/files
```
- **Settings file** copy file ./sites/default/default.settings.php sang ./sites/default/settings.php. Sau đó, phần quyền thư mục lại
```
chown -R root:nginx ./sites/default/settings.php
chmod -R 770 ./sites/default/settings.php
```
Truy cập vào MariaDB và tạo ra 1 database
```
create database drupal;
```
Kiểm tra lại database
```
show database;
```
Tạo ra user dùng để quản lý database
```
create user 'drupal'@'localhost' identified by 'abc@123';
grant all privileges on drupal.* to 'drupal'@'localhost' identified by 'abc@123';
```
Kiểm tra lại user
```
show grants for drupal;
```
Lần lượt điền ô trống với database và user vừa tạo
![](image-kkpqkdww.png)
Tạo tài khoản và mật khẩu cho user quản lý drupal
![](image-kkpqrn9o.png)
![](image-kkpqp306.png)
Sửa các tham số sau đây để có thể upload file kích thước lớn hơn 2M
- **upload_max_filesize** trong /etc/php.ini
- File cấu hình virtualhost thêm dòng **client_max_body_size 50M;**
