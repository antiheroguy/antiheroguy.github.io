---
layout: post
title:  "Khắc phục lỗi The stream or file \"laravel.log\" could not be opened in append mode: failed to open stream: Permission denied"
---

Nếu đã từng làm việc với **Laravel**, ắt hẳn bạn đã từng gặp phải lỗi **The stream or file "laravel.log" could not be opened in append mode: failed to open stream: Permission denied** ít nhất một lần. Nguyên nhân của lỗi này là do Web Server đang sử dụng (Apache, Nginx,...) không có quyền ghi vào file `/storage/logs/laravel.log`. Vậy làm sao để giải quyết được lỗi này một cách triệt để?

## Show me the code
Việc đầu tiên cần làm đó là kiểm tra quyền và owner của thư mục `storage` thông qua lệnh:
~~~bash
ls -lZ
~~~

Nếu Web Server chưa có quyền ghi vào thư mục `storage` nói chung và file `/storage/logs/laravel.log` nói riêng thì cần cấp quyền qua lệnh:
~~~bash
sudo chmod -R 775 storage
~~~
Lưu ý: Đừng bao giờ thay đổi mod về 777 nhé.

Tiếp theo, kiểm tra nếu owner của thư mục `storage` không phải là user đang chạy Web Server thì cần đổi lại owner thông qua lệnh:
~~~bash
sudo chown -R apache storage
~~~
Với **apache** là user đang chạy Web Server. Nếu bạn chưa biết Web Server đang chạy dưới quyền user nào thì có thể kiểm tra bằng các lệnh:
~~~bash
ps aux | egrep '(apache|httpd)'
ps aux | grep nginx
~~~
Ngoài ra cũng cần chỉ định user thực thi cronjob, supervisor hoặc các process liên quan sao cho đúng với owner của thư mục `storage`.

Nếu đã thực hiện những thao tác trên mà vẫn chưa khắc phục được lỗi thì phải kiểm tra **SElinux context** của thư mục `storage`. Cho bạn nào chưa biết thì **SElinux** là một mô-đun bảo mật ở nhân của Linux, cung cấp cơ chế hỗ trợ các chính sách bảo mật kiểm soát truy cập (access control). **SElinux** có 3 chế độ:
* Enforcing: Chế độ mặc định, kiểm soát truy cập thông qua chính sách bảo mật và ghi lại log
* Permissive: Cho phép mọi truy cập nhưng có ghi lại log
* Disabled: Bị vô hiệu hóa

Bonus: Log của **SElinux** được ghi lại tại `/var/log/audit/audit.log`. Bạn có tìm hiểu chi tiết về **SElinux** tại [đây](https://www.computernetworkingnotes.com/linux-tutorials/selinux-explained-with-examples-in-easy-language.html)

Chúng ta có thể kiểm tra trạng thái của **SElinux** bằng lệnh:
~~~bash
sestatus
~~~

Mặc định trên CentOS **SElinux** sẽ được bật ở chế độ Enforcing. Chế độ này chặn quyền ghi vào thư mục `storage` của Laravel. **SElinux** có thể được tắt đi bằng cách chạy lệnh:
~~~bash
setenforce 0
~~~
Tuy nhiên chúng ta không nên làm như vậy mà chỉ nên thay đổi **SElinux context** của thư mục `storage`. Có hai cách để làm việc này:
* Sử dụng `chcon`:
~~~bash
sudo chcon -R -t httpd_sys_rw_content_t storage
~~~
* Sử dụng `semanage`:
~~~bash
sudo semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/html/storage(/.*)?"
~~~
Lưu ý: Với `semanage` phải chỉ định path tuyệt đối tới thư mục `storage` thay vì tương đối. Và sau khi thực thi thì cần xác nhận thay đổi bằng cách chạy lệnh:
~~~bash
sudo restorecon -R -v storage
~~~
Nếu semanage chưa được cài đặt thì có thể cài thông qua các lệnh sau:
~~~bash
# CentOS 7
sudo yum install -y policycoreutils-python

# CentOS 8
sudo dnf install -y policycoreutils-python-utils

# Ubuntu
sudo apt install -y policycoreutils-python-utils
~~~

Về cơ bản cả `chcon` lẫn `semanage` đều cho phép thay đổi **SElinux context** của tệp, thư mục. Sự khác nhau đó là những thay đổi thông qua `chcon` chỉ là tạm thời và có thể được khôi phục lại thông qua lệnh `restorecon` trong khi với `semanage` là vĩnh viễn. Vậy nên tốt hơn hết là sử dụng `semanage` trong việc thay đổi **SElinux context** của tệp, thư mục.

Ngoài ra trong một vài trường hợp cũng cần thực hiện những thao tác trên cho cả thư mục `bootstrap/cache` nữa nhé.