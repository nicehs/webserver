---
title: apache-with-php
tags: []
---

### Part 3: Apache with php
Vì apache có hỗ trợ sẵn module php nên ta không nhất thiết phải cài thêm php-fpm
Tìm kiếm gói mod_php
```
yum search mod_php
```
> php.x86_64 : PHP scripting language for creating dynamic web sites

Cài đặt gói php.x86_64
```
yum install -y php.x86_64
```
Cấu hình apache cho phép biên dịch php
```
...
<VirtualHost test.linux.vn:443>
        DocumentRoot /home/test.linux.vn
        DirectoryIndex index.php index.html
        ...
        <FilesMatch \.php$>
                SetHandler application/x-httpd-php
        </FilesMatch>
</VirtualHost>
```
Tạo file php /home/test.linux.vn/index.php
```
<?php
    echo "this is apache with php";
?>
```
![](image-kkyy7kfi.png)
