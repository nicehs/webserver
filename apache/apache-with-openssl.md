---
title: apache-with-openssl
tags: []
---

### Part 2: Apache with openssl
Đầu tiên, tạo key và certificate [xem chi tiết](https://github.com/nicehs/webserver/blob/main/openssl.md)  
Cài đặt thêm module ssl để apache có thê hiểu được các thông số cấu hinh ssl  
```
yum search mod_ssl
yum instal mod_ssl.x86_64
```
Chỉnh sửa lại file cấu hình Apache /etc/httpd/conf/httpd.conf
```
# Thêm vào dòng sau
Listen 443 https
# Comment hết các dòng hoặc đổi tên file /etc/httpd/conf.d/ssl.conf
```
Tạo VirtualHost mới
```
SSLPassPhraseDialog exec:/usr/libexec/httpd-ssl-pass-dialog
<VirtualHost test.linux.vn:443>
        DocumentRoot /home/test.linux.vn
        DirectoryIndex index.html
        ServerName test.linux.vn
        ServerAdmin admin@linux.vn
        <Directory /home/>
                AllowOverride none
                Require all granted
        </Directory>
        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/test.linux.vn.crt
        SSLCertificateKeyFile /etc/pki/tls/private/test.linux.vn.key
</VirtualHost>
```
- SSLPassPhraseDialog dùng để nhập passphrase nếu passphrase đã bị xoá thì tham số này không cần thiết
- test.linux.vn:443 sử dụng cổng 443 cho dịch vụ https
- SSLEngine on cho phép sử dụng ssl/tls
- SSLCertificateFile chỉ ra đường dẫn tới certificate
- SSLCertificateKeyFile chỉ ra đường dẫn tới key
- Các tham số còn lại tương tự như phần [apache-with-http](https://github.com/nicehs/webserver/blob/main/apache/apache-with-http.md)

Kiểm tra lại dịch vụ
```
httpd -t
```
Khởi động lại dịch vụ
```
systemctl restart httpd
```
Cho phép cổng 443
```
firewall-cmd --add-port=443/tcp --permanent
firewall-cdm --reload
```
![](image-kky7oyff.png)
