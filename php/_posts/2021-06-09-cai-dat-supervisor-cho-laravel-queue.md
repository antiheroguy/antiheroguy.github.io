---
layout: post
title:  "Cài đặt Supervisor cho Laravel Queue"
---

**Laravel Queue** là một công cụ mạnh mẽ cho phép thực thi nhiều process cùng lúc trong background. Nó vô cùng hữu ích trong việc làm giảm thời gian thực thi cũng như giúp cấu trúc code trở nên tốt hơn. Tiện dụng là vậy, song **Laravel Queue** cũng có một số nhược điểm, như có thể bị dừng chạy nếu có lỗi xảy ra, hoặc không thể tự khởi động lại mỗi khi có sự thay đổi trong ứng dụng. Đây là lúc mà chúng ta cần đến một **Process Monitor**, và **Supervisor** chính là một trong số đó. 

## Show me the code

### Cài đặt Supervisor
Chúng ta có thể cài đặt Supervisor trên Ubuntu thông qua câu lệnh
~~~bash
sudo apt install supervisor
~~~
Trong trường hợp sử dụng Centos thì cần phải cài đặt **epel-release** trước
~~~bash
sudo yum install epel-release
~~~
Riêng với Centos trên Amazon EC2 thì **epel-release** phải được cài đặt thông qua **Amazon Linux Extra**
~~~bash
sudo amazon-linux-extras install epel
~~~
Còn với dùng Windows, có thể cài đặt Supervisor thông qua **pip**
~~~bash
pip install supervisor
~~~

### Cấu hình Supervisor
Thông thường file cấu hình của Supervisor sẽ nằm ở `/etc/supervisord.conf`. Nếu file này chưa tồn tại, có thể tạo mới với thông số mặc định thông qua lệnh
~~~bash
echo_supervisord_conf > /etc/supervisord.conf
~~~
Có thể thấy dòng **include** ở cuối cùng cho phép chúng ta tách cấu hình Supervisor ra thành nhiều file riêng biệt
~~~ini
[include]
files = supervisord.d/*.ini
~~~
Tiếp theo, chúng ta sẽ tạo một file có tên là **laravel-worker.ini** ở trong thư mục **supervisord.d** và lưu lại với nội dung như sau
~~~ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=/usr/bin/php /var/www/html/artisan queue:work --sleep=3 --tries=3
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=root
numprocs=3
redirect_stderr=true
stdout_logfile=/var/www/html/storage/logs/queue.log
stopwaitsecs=3600
~~~
Trong đó, **numprocs** = 3 đại diện cho việc Supervisor sẽ chạy 3 process `queue:work` và giám sát chúng, tự khởi động lại nếu có lỗi xảy ra. Cũng cần chỉ định đúng đường dấn đến file **artisan** nằm trong folder dự án ở mục **command**, cũng như nơi lưu **logfile**. Các cấu hình khác có thể tham khảo ở [đây](http://supervisord.org/configuration.html)

Sau khi cập nhật thay đổi, chúng ta cần phải khởi động process lên bằng cách chạy
~~~bash
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start laravel-worker:*
~~~
Nếu gặp phải lỗi:
~~~bash
error: <class 'socket.error'>, [Errno 2] No such file or directory: file: /usr/lib64/python2.7/socket.py line: 228
~~~
Có nghĩa là Supervisor chưa được start lên, chỉ cần chạy lệnh sau để restart lại
~~~bash
sudo service supervisord restart
~~~
Chúng ta có thể kiểm tra trạng thái monitor của Supervisor thông qua câu lệnh
~~~bash
sudo supervisorctl status
~~~
Output hiển thị như dưới đây là đã thành công
~~~bash
laravel-worker:laravel-worker_00   RUNNING   pid 1233, uptime 0:00:30
laravel-worker:laravel-worker_01   RUNNING   pid 1234, uptime 0:00:30
laravel-worker:laravel-worker_02   RUNNING   pid 1235, uptime 0:00:30
~~~
Ngoài ra, chúng ta có thể cài đặt **Redis** để sử dụng làm **Queue Driver** cho Laravel Queue
~~~bash
sudo apt install redis-server
~~~
Lưu ý: Với Centos thì cần phải cài đặt **epel-release** trước khi cài Redis
Sau khi đã cài đặt thành công, tiến hành khởi động Redis
~~~bash
sudo systemctl start redis.service
~~~
Có thể sử dụng câu lệnh **enable** để thiết lập Redis khởi động cùng hệ thống
~~~bash
sudo systemctl enable redis-server
~~~
Để sử dụng Redis làm Queue Driver cho Laravel, mở file **.env** và sửa lại như sau
~~~ini
QUEUE_CONNECTION=redis
~~~
Nếu trong quá trình thực thi Job gặp phải lỗi
~~~bash
Class 'Predis\Client' not found
~~~
Có nghĩa là thư viện predis/predis chưa được cài đặt
~~~bash
composer require predis/predis
~~~