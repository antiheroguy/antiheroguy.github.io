---
layout: post
title: "Debug PHP với XDebug trong VSCode"
---

Debugging là một bước không thể thiếu trong quá trình phát triển phần mềm, giúp lập trình viên phát hiện và khắc phục lỗi nhanh chóng. Xdebug là một công cụ mạnh mẽ dành cho PHP, hỗ trợ bạn debug mã nguồn một cách hiệu quả. Bài viết này sẽ hướng dẫn bạn cách cài đặt và cấu hình Xdebug để sử dụng với VS Code.

# Show me the code

## Cài Đặt Xdebug

**Bước 1:** Kiểm tra phiên bản PHP và cài đặt Xdebug tương ứng.

Bạn có thể kiểm tra phiên bản PHP bằng lệnh sau:

~~~bash
php -v
~~~

**Bước 2:** Cài đặt Xdebug.

Cách cài đặt Xdebug sẽ khác nhau tùy thuộc vào hệ điều hành và phiên bản PHP. Bạn có thể cài đặt Xdebug thông qua `pecl`:

~~~bash
pecl install xdebug
~~~

Hoặc nếu bạn dùng Ubuntu, bạn có thể cài đặt thông qua `apt`:

~~~bash
sudo apt-get install php-xdebug
~~~

**Bước 3:** Kích hoạt Xdebug trong file cấu hình PHP (`php.ini`).

Tìm vị trí file `php.ini` bằng lệnh:

~~~bash
php --ini
~~~

Thêm hoặc cập nhật các dòng cấu hình sau vào `php.ini`:

~~~ini
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=127.0.0.1
xdebug.client_port=9003
xdebug.log=/tmp/xdebug.log
~~~

- `xdebug.mode=debug`: Kích hoạt chế độ debug.
- `xdebug.start_with_request=yes`: Xdebug sẽ khởi động cùng với mỗi yêu cầu.
- `xdebug.client_host=127.0.0.1`: Địa chỉ IP của máy client (VS Code).
- `xdebug.client_port=9003`: Cổng để VS Code và Xdebug kết nối (mặc định là 9003).

## Cấu Hình VS Code Để Debug

**Bước 1:** Cài đặt extension "PHP Debug" từ Felix Becker trong VS Code.

- Mở VS Code, đi đến phần Extensions (`Ctrl + Shift + X`).
- Tìm kiếm "PHP Debug" và cài đặt extension.

**Bước 2:** Cấu hình `launch.json` trong VS Code.

- Mở thư mục dự án của bạn trong VS Code.
- Tạo thư mục `.vscode` nếu chưa có, và tạo file `launch.json` với nội dung sau:

~~~json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/path/to/your/php/files": "${workspaceFolder}"
            }
        }
    ]
}
~~~

- `pathMappings`: Chỉ định đường dẫn của mã nguồn PHP trên máy so với VS Code.

**Bước 3:** Đặt breakpoint trong file PHP và bắt đầu debug.

- Đặt breakpoint bằng cách nhấn vào bên trái dòng mã mà bạn muốn dừng.
- Chọn "Run and Debug" từ thanh bên trái (`Ctrl + Shift + D`).
- Chọn cấu hình "Listen for Xdebug" và nhấn "Start Debugging".

## Cài Đặt Xdebug Cho Ứng Dụng PHP Sử Dụng Docker Compose

Nếu bạn đang sử dụng Docker Compose để chạy ứng dụng PHP, bạn cần cấu hình Xdebug trong Docker.

### Cấu Hình Docker Compose

**Bước 1:** Thêm Xdebug vào Dockerfile của PHP.

Trong Dockerfile, thêm các dòng sau để cài đặt và cấu hình Xdebug:

~~~dockerfile
# Dockerfile
FROM php:8.1-fpm

RUN pecl install xdebug && docker-php-ext-enable xdebug

COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
~~~

**Bước 2:** Tạo file `xdebug.ini` trong thư mục dự án (cùng cấp với Dockerfile) với nội dung:

~~~ini
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
xdebug.log=/tmp/xdebug.log
~~~

- `xdebug.client_host=host.docker.internal`: Đảm bảo Xdebug kết nối được với VS Code trên máy host.

**Bước 3:** Cập nhật file `docker-compose.yml`.

Thêm các cổng cần thiết để Xdebug có thể kết nối với máy host:

~~~yaml
# docker-compose.yml
version: '3.8'

services:
  php:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - .:/var/www/html
    ports:
      - "9003:9003"
    environment:
      XDEBUG_MODE: debug
~~~

### Cấu Hình VS Code Để Debug Với Docker

Tương tự như phần cài đặt thủ công, bạn cần cấu hình `launch.json` trong VS Code:

**Bước 1:** Mở thư mục dự án trong VS Code.

**Bước 2:** Tạo thư mục `.vscode` nếu chưa có, và tạo file `launch.json` với nội dung sau:

~~~json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug (Docker)",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html": "${workspaceFolder}"
            }
        }
    ]
}
~~~

- `pathMappings`: Liên kết đường dẫn mã nguồn PHP trong container Docker với thư mục làm việc của bạn trên máy host.

**Bước 3:** Đặt breakpoint trong mã PHP và bắt đầu debug như đã hướng dẫn ở phần trên.

Xdebug là một công cụ tuyệt vời giúp bạn kiểm soát và phân tích mã PHP một cách hiệu quả. Việc cấu hình và sử dụng Xdebug với VS Code không quá phức tạp, dù bạn cài đặt ứng dụng PHP thủ công hay sử dụng Docker Compose. Hy vọng bài viết này giúp bạn thiết lập môi trường debug dễ dàng hơn. Nếu bạn có bất kỳ câu hỏi hoặc thắc mắc nào, đừng ngần ngại để lại bình luận dưới bài viết này!
