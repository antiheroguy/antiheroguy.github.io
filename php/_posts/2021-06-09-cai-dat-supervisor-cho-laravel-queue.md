---
layout: post
title:  "Cài đặt Supervisor cho Laravel Queue"
---

**Supervisor** là một công cụ giám sát process tuyệt vời, đặc biệt hữu ích khi làm việc với **Laravel Queue**. Laravel Queue cho phép xử lý nhiều tác vụ cùng lúc dưới nền, giúp giảm thời gian xử lý và cải thiện cấu trúc mã. Tuy nhiên, Laravel Queue có thể ngừng hoạt động khi gặp lỗi hoặc không tự khởi động lại sau các thay đổi trong ứng dụng. Đây chính là lúc **Supervisor** trở thành giải pháp để giám sát và tự động khởi động lại các process khi cần thiết.

## Cài đặt Supervisor

Để cài đặt Supervisor trên Ubuntu, sử dụng lệnh:

~~~bash
sudo apt install supervisor
~~~

Nếu bạn sử dụng CentOS, cần cài đặt **epel-release** trước:

~~~bash
sudo yum install epel-release
~~~

Trên CentOS chạy trên Amazon EC2, bạn sẽ cần cài đặt **epel-release** thông qua **Amazon Linux Extras**:

~~~bash
sudo amazon-linux-extras install epel
~~~

Trên Windows, Supervisor có thể được cài đặt qua **pip**:

~~~bash
pip install supervisor
~~~

## Cấu hình Supervisor

Thông thường, file cấu hình chính của Supervisor sẽ nằm tại `/etc/supervisord.conf`. Nếu file này chưa tồn tại, bạn có thể tạo mới với cấu hình mặc định bằng lệnh:

~~~bash
echo_supervisord_conf > /etc/supervisord.conf
~~~

Bên trong file cấu hình, phần **include** ở cuối cho phép bạn tách cấu hình thành nhiều file riêng biệt:

~~~ini
[include]
files = supervisord.d/*.ini
~~~

Tiếp theo, tạo file **laravel-worker.ini** trong thư mục **supervisord.d** với nội dung sau:

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

Trong đó:
- **numprocs=3**: Supervisor sẽ chạy và giám sát 3 process `queue:work`, tự động khởi động lại nếu có lỗi.
- **command**: Đảm bảo chỉ định đúng đường dẫn đến file **artisan** trong thư mục dự án của bạn.
- **stdout_logfile**: Chỉ định nơi lưu log file.

Để biết thêm chi tiết về các cấu hình, bạn có thể tham khảo [tài liệu chính thức của Supervisor](http://supervisord.org/configuration.html).

Sau khi cấu hình, khởi động các process với các lệnh:

~~~bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start laravel-worker:*
~~~

Nếu gặp lỗi như sau:

~~~bash
error: <class 'socket.error'>, [Errno 2] No such file or directory: file: /usr/lib64/python2.7/socket.py line: 228
~~~

Điều này có nghĩa là Supervisor chưa được khởi động. Chạy lệnh sau để khởi động lại Supervisor:

~~~bash
sudo service supervisord restart
~~~

Kiểm tra trạng thái của các process với lệnh:

~~~bash
sudo supervisorctl status
~~~

Nếu kết quả hiển thị như dưới đây, Supervisor đã hoạt động thành công:

~~~bash
laravel-worker:laravel-worker_00   RUNNING   pid 1233, uptime 0:00:30
laravel-worker:laravel-worker_01   RUNNING   pid 1234, uptime 0:00:30
laravel-worker:laravel-worker_02   RUNNING   pid 1235, uptime 0:00:30
~~~

## Cài đặt Redis cho Laravel Queue

Để sử dụng **Redis** làm **Queue Driver** cho Laravel, bạn có thể cài đặt Redis trên Ubuntu với lệnh:

~~~bash
sudo apt install redis-server
~~~

Trên CentOS, cần cài đặt **epel-release** trước khi cài Redis:

~~~bash
sudo yum install epel-release
sudo yum install redis
~~~

Khởi động Redis sau khi cài đặt thành công:

~~~bash
sudo systemctl start redis.service
~~~

Để Redis khởi động cùng hệ thống:

~~~bash
sudo systemctl enable redis-server
~~~

Để sử dụng Redis làm Queue Driver cho Laravel, mở file **.env** và chỉnh sửa như sau:

~~~ini
QUEUE_CONNECTION=redis
~~~

Nếu gặp lỗi sau khi sử dụng Redis:

~~~bash
Class 'Predis\Client' not found
~~~

Bạn cần cài đặt thư viện **predis/predis** bằng Composer:

~~~bash
composer require predis/predis
~~~

Với các bước trên, Supervisor sẽ giúp bạn giám sát và tự động khởi động lại các process của Laravel Queue, đảm bảo hệ thống luôn hoạt động ổn định và hiệu quả.
