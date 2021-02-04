---
title: Cài đặt nginx cơ bản
tags: []
---

### **Phần 1: Cài đặt NGINX**
Cài đặt gói epel-release để cập nhật repo mới nhất
```
# yum install -y epel-release
```
Kiểm tra lại epel-release hiện đã được cài đặt trên máy hay chưa bằng lệnh
```
# rpm -qa | grep epel-release
```
Nếu hiển thị _**epel-release**-x.noarch_ (với x là phiên bản của gói) thì gói đã được cài đặt  
Tìm kiếm gói nginx cần cài
```
# yum search nginx
```
Sẽ thấy dòng
> nginx.x86_64 : A high performance web server and reverse proxy server  

Cài đặt nginx
```
# yum install -y nginx.x86_64
```
Kiểm tra nginx xem đã được cài đặt chưa
```
# rpm -qa | grep nginx
```
Cấu hình file /etc/nginx/nginx.conf
```
# vi /etc/nginx/nginx.conf
```
- Vì ta sẽ tạo 1 virtualhost riêng nên sẽ comment vitualhost mặc định
- Comment từ dòng 38 đến 57  

Tạo virtualhost mới tại đường đẫn /etc/nginx/conf.d/virtualhost.conf
- **Lưu ý**: tên file virtualhost.conf có thể đặt theo tên miền được cấu hình trong file để thuận tiện cho việc kiểm tra và dễ dàng ghi nhớ. Mỗi virtualhost mà được cấu hình nên đặt ở các file khác nhau. Ở ví dụ này ta sẽ lấy tên miền là drupal.linex.vn nên tên file có thể là drupal.linex.conf
```
server {
        listen 80;
        server_name drupal.linex.vn;
        root /home/drupal.linex.vn;
        index index.php;
}
```
- **listen**: là cổng mà nginx lắng nghe traffic từ client
- **server_name**: tên miền của trang web
- **root**: chỉ ra thư mục chứa source code của website
- **index**: chỉ ra file để hiện thị mặc định khi vào trang web

Tạo file html /home/drupal.linex.vn/index.html
```
<h3> this is drupal.linex.vn </h3>
```
Quyền truy cập vào thư mục root:
- Cho phép owner là tài khoản người dùng với toàn quyền (với ví dụ này tài khoản đang là root)
- Với nhóm là nginx chỉ cần quyền thực thi và đọc
- Các nhóm còn lại không liên quan ta không cho quyền
```
chown -R root:nginx /home/drupal.linex.vn
chmod -R 750 /home/drupal.linex.vn
```
Kiểm tra lại quyền thư mục  
```
# ls -l /home/drupal.linex.vn
```
> drwxr-x---. 2 root nginx 24 Feb  3 20:04 drupal.linex.vn

Kiểm tra lại cấu hình
```
# nginx -t
```
Hiển thị như dưới thì ta đã cấu hình đúng
> nginx: the configuration file /etc/nginx/nginx.conf syntax is ok  
> nginx: configuration file /etc/nginx/nginx.conf test is successful

Khởi động dịch vụ  
```
# systemctl start nginx
# systemctl enable nginx
```
Hãy đảm bảo máy chủ đã tắt selinux vì có thể selinux sẽ chặn không cho dịch vụ truy cập vào thư mục. Kiểm tra:  
```
# sestatus
```
Muốn tắt selinux sửa tham số SELINUX thành disabled trong file /etc/selinux/config rồi khởi động lại server. Để chắc chắn sau khi việc khởi động hoàn tất ta sẽ kiểm tra lại lần nữa

Mặc định khi khởi động server, dịch vụ tường lửa sẽ chạy, nếu không khai báo cho phép dịch vụ đi qua tường lửa thì dịch vụ sẽ bị tường lửa chặn port lại

```
# firewall-cmd --add-port=80/tcp --permanent
# firewall-cmd --reload
```
Mở trình duyệt truy cập website drupal.linex.vn  
![](image-kkp47ll5.png)  
- **Lưu ý**: để có thể dùng tên miền để truy cập trang web ta cần đảm bảo tên miền đã được phân giải. Nếu không có DNS server ta có thể chỉnh sửa file host để test
