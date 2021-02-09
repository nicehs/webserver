---
title: openssl
tags: []
---

Tìm đúng gói openssl
```
yum search openssl
```
Ta sẽ thấy 2 gói nhìn có vẻ giống nhau
> openssl.x86_64 : Utilities from the general purpose cryptography library with TLS implementation
> openssl11.x86_64 : Utilities from the general purpose cryptography library with TLS implementation

- openssl11 là phiên bản 1.1.1 của openssl hỗ trợ TLS v1.3 ([xem chi tiết](https://www.openssl.org/blog/blog/2018/09/11/release111/))

Nếu không quan tâm đến TLSv1.3 thì ta cài gói nào cũng được
```
yum install openssl11.x86_64
```
Kiểm tra lại
```
rpm -qa | grep openssl
```
> openssl11-libs-1.1.1g-2.el7.x86_64  
> openssl11-1.1.1g-2.el7.x86_64

Tạo private key
```
openssl genrsa -des3 -out /etc/pki/tls/private/test.linux.vn.key 4096
```
Tạo file csr (Certificate Signing Request)
```
cd /etc/pki/tls/private
openssl req -new -key test.linux.vn.key -out test.linux.vn.csr
```
Điền các thông tin về certificate:
> Country Name (2 letter code) [XX]:VN  
State or Province Name (full name) []:HD  
Locality Name (eg, city) [Default City]:HN  
Organization Name (eg, company) [Default Company Ltd]:linux.vn 
Organizational Unit Name (eg, section) []:linux.vn  
Common Name (eg, your name or your server's hostname) []:test.linux.vn 
Email Address []:admin@linux.vn

- **Lưu ý**: phần Commom Name phải điền đúng tên miền

Tạo file .crt
```
openssl x509 -req -days 365 -in test.linux.vn.csr -signkey test.linux.vn.key -out /etc/pki/tls/certs/test.linux.vn.crt
```
Kiểm tra lại thông tin certificate
```
openssl x509 -in /etc/pki/tls/certs/test.linux.vn.crt -text
```
Đổi lại quyền
```
chown -R root:apache /etc/pki/tls/private/*
chown -R root:apache /etc/pki/tls/certs/test.linux.vn.crt
chmod -R 750 /etc/pki/tls/private/*
chmod -R 750 /etc/pki/tls/certs/test.linux.vn.crt
```
- Cho quyền với owner là user mà bạn đang xài sẽ có full quyền để có thể chỉnh sửa thư mục
- Group là apache chỉ cần quyền đọc và thực thi là có thể chạy được
- Các user còn lại không liên quan ta không cho quyền
