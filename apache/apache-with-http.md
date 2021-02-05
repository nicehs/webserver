---
title: apache-with-http
tags: []
---

### Part 1: Apache with http
Tìm kiếm đúng gói apache
```
yum search httpd
```
> httpd.x86_64 : Apache HTTP Server

Cài đặt gói apache
```
yum install httpd.x86_64
```
Vì chúng ta cấu hình 1 VirtualHost mới nên phải thay đổi cấu hình mặc định trong file /etc/httpd/conf/httpd.conf. Comment các dòng sau.
```
119 #DocumentRoot "/var/www/html"
124 #<Directory "/var/www">
125 #    AllowOverride None
126     # Allow open access:
127 #    Require all granted
128 #</Directory>
131 #<Directory "/var/www/html">
144 #    Options Indexes FollowSymLinks
151 #    AllowOverride None
156 #    Require all granted
156 #    Require all granted
157 #</Directory>
```
Cấu hình file virtualhost mới /etc/httpd/conf.d/test.linux.vn.conf
- **Lưu ý**: để tiện cho việc tìm kiếm sau này ta nên đặt tên file trùng với tên miền của trang web sắp dựng. Ở ví dụ này ta sẽ dùng tên miền là test.linux.vn.
```
<VirtualHost test.linux.vn:80>
        DocumentRoot /home/test.linux.vn
        DirectoryIndex index.html
        ServerName test.linux.vn
        ServerAdmin admin@linux.vn
        <Directory /home/>
                AllowOverride none
                Require all granted
        </Directory>
</VirtualHost>
```
- **test.linux.vn:80**: chỉ ra cổng sử dụng cho web
- **DocumentRoot**: thư mục đặt source code dùng để chạy trang web
- **DirectoryIndex**: file hiển thị mặc định khi vào trang web
- **ServerName**: tên miền cho trang web
- **ServerAdmin**: địa chỉ email của người quản trị trang web
- Có thể thay thế thư mục /home bằng bất cứ thư mục nào tuỳ ý
- AllowOverride: cho phép hoặc không cho apache ghi đè lên dữ liệu hệ thống (xem [chi tiết](https://httpd.apache.org/docs/2.4/mod/core.html#allowoverride))
- **Require**: cho phép tất cả hoặc chỉ cho phép địa chỉ cụ thể sử dụng thư mục /home (xem [chi tiết](https://httpd.apache.org/docs/2.4/howto/access.html)) 

Tạo thư mục /home/test.linux.vn
```
mkdir /home/test.liunx.vn
```
Tạo 1 file /home/test.linux.vn/index.html
```
<h3> This is Apache with html <h3>
```
Chỉnh sửa cấu hình file host, để có thể truy cập trang web. Mở trình duyệt và test
![](image-kkrruodz.png)
