---
title: database-mariadb
tags: []
---

### Phần 4: Cài đặt MariaDB
Tìm kiếm gói mariadb
```
yum search mariadb
```

Cài gói mariadb-server.x86_64
```
yum install -y mariadb-server.x86_64
```
Kiểm tra gói
```
rpm -qa | grep mariadb
```
Khởi động dịch vụ
```
systemctl start mariadb
systemctl enable mariadb
```
Cài đặt MariaDB:
```
mysql_secure_installation

Set root password? [Y/n] y
New password: #Nhập pass
Re-enter new password:

Remove anonymous users? [Y/n] y

Disallow root login remotely? [Y/n] y

Remove test database and access to it? [Y/n] y

Reload privilege tables now? [Y/n] y
```
Login vào database
```
mysql -u root -p
Enter password: #Nhập pass
```
