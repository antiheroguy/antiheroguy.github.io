---
layout: post
title:  "Khắc phục lỗi The stream or file \"laravel.log\" could not be opened in append mode: failed to open stream: Permission denied"
---

Khi làm việc với **Laravel**, có thể bạn đã gặp lỗi **"The stream or file 'laravel.log' could not be opened in append mode: failed to open stream: Permission denied"** ít nhất một lần. Lỗi này xảy ra khi Web Server (Apache, Nginx,...) không có quyền ghi vào file `/storage/logs/laravel.log`. Hãy cùng tìm hiểu cách khắc phục lỗi này một cách chi tiết và triệt để.

# Show me the code

## Kiểm Tra Quyền và Chủ Sở Hữu của Thư Mục `storage`

Đầu tiên, kiểm tra quyền và chủ sở hữu (owner) của thư mục `storage` bằng lệnh:

~~~bash
ls -lZ storage
~~~

Nếu Web Server không có quyền ghi vào thư mục `storage` hoặc file `/storage/logs/laravel.log`, bạn cần cấp quyền ghi bằng lệnh:

~~~bash
sudo chmod -R 775 storage
~~~

> **Lưu ý:** Không nên đặt quyền 777 vì điều này có thể gây ra lỗ hổng bảo mật nghiêm trọng.

Tiếp theo, kiểm tra chủ sở hữu của thư mục `storage`. Nếu không phải là user đang chạy Web Server, bạn cần thay đổi chủ sở hữu bằng lệnh:

~~~bash
sudo chown -R apache:apache storage
~~~

Thay **apache** bằng tên user chạy Web Server của bạn. Nếu không biết user nào đang chạy Web Server, bạn có thể kiểm tra bằng:

~~~bash
ps aux | egrep '(apache|httpd|nginx)'
~~~

Ngoài ra, đảm bảo rằng các tiến trình liên quan như cronjob, supervisor cũng chạy dưới user phù hợp với chủ sở hữu của thư mục `storage`.

## Kiểm Tra và Cấu Hình SELinux

Nếu đã chỉnh sửa quyền và chủ sở hữu mà vẫn gặp lỗi, vấn đề có thể liên quan đến **SELinux** - một mô-đun bảo mật của Linux, kiểm soát các chính sách bảo mật về truy cập. **SELinux** có ba chế độ:

- **Enforcing**: Kiểm soát truy cập theo chính sách và ghi lại log.
- **Permissive**: Cho phép tất cả truy cập nhưng vẫn ghi lại log.
- **Disabled**: SELinux bị vô hiệu hóa.

Để kiểm tra trạng thái của SELinux, sử dụng lệnh:

~~~bash
sestatus
~~~

Mặc định trên CentOS, SELinux thường bật ở chế độ Enforcing, có thể gây cản trở quyền ghi vào thư mục `storage` của Laravel. Bạn có thể tạm thời tắt SELinux bằng lệnh:

~~~bash
sudo setenforce 0
~~~

> **Lưu ý:** Tắt SELinux không phải là giải pháp tốt nhất vì có thể giảm mức độ bảo mật của hệ thống. Thay vào đó, chỉ nên thay đổi **context** của thư mục `storage` để phù hợp với yêu cầu của Web Server.

### Thay Đổi SELinux Context

Có hai cách để thay đổi SELinux context:

#### Sử Dụng `chcon`

Lệnh `chcon` giúp thay đổi context của thư mục `storage` một cách tạm thời:

~~~bash
sudo chcon -R -t httpd_sys_rw_content_t storage
~~~

#### Sử Dụng `semanage`

Lệnh `semanage` thay đổi context một cách vĩnh viễn:

~~~bash
sudo semanage fcontext -a -t httpd_sys_rw_content_t "/path/to/your/laravel/project/storage(/.*)?"
~~~

Sau đó, áp dụng thay đổi với lệnh:

~~~bash
sudo restorecon -R -v storage
~~~

> **Lưu ý:** Khi sử dụng `semanage`, bạn cần chỉ định đường dẫn tuyệt đối tới thư mục `storage`. Nếu `semanage` chưa được cài đặt, bạn có thể cài đặt nó bằng:

~~~bash
sudo yum install -y policycoreutils-python-utils
# hoặc với Ubuntu
sudo apt install -y policycoreutils-python-utils
~~~

## Áp Dụng Cho Thư Mục Khác

Ngoài thư mục `storage`, đôi khi bạn cũng cần thực hiện các thao tác tương tự cho thư mục `bootstrap/cache`, đặc biệt khi gặp các lỗi liên quan đến quyền truy cập.

Việc gặp lỗi quyền truy cập với Laravel là rất phổ biến, đặc biệt trong các môi trường bảo mật cao như CentOS với SELinux. Thay vì tắt các tính năng bảo mật, hãy điều chỉnh cấu hình và quyền truy cập phù hợp để đảm bảo ứng dụng của bạn hoạt động trơn tru và an toàn.
