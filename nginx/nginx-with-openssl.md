---
title: nginx-with-https
tags: []
---

### Phần 2: Nginx với SSL/TLS
Để tăng độ bảo mật cho website ta sẽ nâng website lên thành https. Để nâng website lên thành https ta cần có phải có key và certificate cho website. Các cách để có key và certificate:
- Nếu website public thì có thể tạo với certbot (miễn phí) xịn hơn nữa thì có thể thì phải đăng ký với Certificate Authority Providers. 1 số Certificate Authority Providers: [GoDaddy](https://vn.godaddy.com/), [Symantec](https://securitycloud.symantec.com/), [Networksolution](https://www.networksolutions.com/),...
- Cách khác đó là seft-sign certificate tức là ta tự tạo ra key với openssl

Trong ví dụ này ta sẽ dùng OpenSSL để tạo key và certificate

Kiểm tra gói openssl
```
yum search openssl
```
- Sẽ thấy 2 gói openssl.x86_64 và openssl11.x86_64.
- OpenSSL11 là phiên bản mới ([OpenSSL 1.1.1](https://www.openssl.org/blog/blog/2018/09/11/release111/)) có hỗ trợ TLS 1.3 và 1 số kiểu mã hoá mới khác 

Cài đặt openssl11.x86_64

```
yum install openssl11.x86_64
```
Kiểm tra lại gói openssl11.x86_64
```
rpm -qa | grep openssl
```
> openssl11-libs-1.1.1g-2.el7.x86_64  
> openssl11-1.1.1g-2.el7.x86_64

Tạo private key
```
openssl genrsa -des3 -out /etc/pki/tls/private/drupal.linex.vn.key 4096
```
Tạo file csr (Certificate Signing Request)
```
openssl req -new -key drupal.linex.vn.key -out drupal.linex.vn.csr
```
Điền các thông tin về certificate:
> Country Name (2 letter code) [XX]:VN  
State or Province Name (full name) []:HD  
Locality Name (eg, city) [Default City]:HN  
Organization Name (eg, company) [Default Company Ltd]:linex.vn 
Organizational Unit Name (eg, section) []:linex.vn  
Common Name (eg, your name or your server's hostname) []:drupal.linex.vn  
Email Address []:admin@linex.vn

- **Lưu ý**: phần Commom Name phải điền đúng tên miền

Tạo file .crt
```
openssl x509 -req -days 365 -in drupal.linex.vn.csr -signkey drupal.linex.vn.key -out /etc/pki/tls/certs/drupal.linex.vn.crt
```
Kiểm tra lại thông tin certificate
```
openssl x509 -in /etc/pki/tls/certs/drupal.linex.vn.crt -text
```
Đổi lại quyền
```
chown -R root:nginx /etc/pki/tls/private/*
chown -R root:nginx /etc/pki/tls/certs/drupal.linex.vn.crt
chmod -R 750 /etc/pki/tls/private/*
chmod -R 750 /etc/pki/tls/certs/drupal.linex.vn.crt
```
Sửa lại file cấu hình 
```
ssl_password_file /var/lib/nginx/ssl_password.txt;
server {
        listen 443 ssl;
        server_name drupal.linex.vn;
        root /home/drupal.linex.vn;
        index index.html;
        ssl_certificate         /etc/pki/tls/certs/drupal.linex.vn.crt;
        ssl_certificate_key     /etc/pki/tls/private/drupal.linex.vn.key;
}
```
- **Lưu ý**: cần phải tạo file ssl_password.txt để lưu passphrase

Thêm port 443 vào firewall và khởi động lại dịch vụ
```
systemctl restart nginx
firewall-cmd --add-port=443/tcp --permanent
firewall-cmd --reload
```
Mở trình duyệt vào địa chỉ https://drupal.linex.vn để kiểm tra
![](image-kkpat43p.png)
