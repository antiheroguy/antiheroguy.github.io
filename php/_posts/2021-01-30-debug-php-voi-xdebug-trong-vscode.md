---
layout: post
title: "Debug PHP với XDebug trong VSCode"
---

**Debug** (gỡ lỗi) là một phần không thể thiếu trong quá trình phát triển phần mềm. Trong khi các developer .Net hay Java thường có trong tay một loạt những tool, IDE hỗ trợ việc debug một cách hoàn chỉnh, các developer PHP lại không có nhiều sự lựa chọn như vậy. Nếu bạn không có nhiều kinh nghiệm trong việc debug các ứng dụng, chắc chắn bạn sẽ phải sử dụng các hàm `error_log()` và `var_dump()` để kiểm tra rất nhiều biến của mình. May mắn thay, có một vài công cụ mã nguồn mở cung cấp cho các nhà phát triển PHP khả năng debug tương tự như của một IDE đắt tiền. VSCode chính là một trong số đó. Bài viết này sẽ hướng dẫn các bạn cách thiết lập XDebug trong VSCode để debug một ứng dụng PHP.

## Show me the code
[XDebug](https://xdebug.org/) là một **extension** (phần mở rộng) của PHP, giúp các nhà phát triển debug và phát triển các dự án của họ một cách suôn sẻ. XDebug cho phép chúng ta thêm các **breakpoint** (điểm dừng) trong code và tạm dừng việc thực thi code để có thể kiểm tra giá trị của các biến tại mỗi breakpoint. 

### Cài đặt XDebug
Cách cài đặt XDebug hết sức đơn giản. Đối với người dùng Linux hay Mac, chỉ cần thực hiện các lệnh dưới đây:

#### Mac
~~~bash
pecl install debug
~~~

### Linux
~~~bash
sudo apt install php-xdebug
~~~

### Windows
Với người dùng Windows thì việc cài đặt phức tạp hơn một chút. Rất may, XDebug đã cung cấp một công cụ nhằm hỗ trợ người dùng Windows tích hợp XDebug vào phiên bản PHP đang sử dụng, đó là [XDebug Wizard](https://xdebug.org/wizard). Để sử dụng XDebug Wizard, đầu tiên các bạn cần mở terminal lên và gõ lệnh:
~~~bash
php -i
~~~
Chúng ta cần lấy output từ lệnh này và paste vào form của XDebug Wizard. Người dùng Windows có thể sử dụng **clip.exe** để nhanh chóng copy output của một lệnh bất kì vào clipboard:
~~~bash
php -i | clip
~~~
Sau khi hoàn thành thì ấn vào nút **Analyse my phpinfo() output**. XDebug Wizard sẽ phân tích output của bạn và đưa ra chỉ dẫn cụ thể để cài đặt XDebug. Ví dụ máy bạn cài **xampp** và **PHP 7.4** thì các bước thực hiện sẽ được đưa ra như sau:

 1. Download php_xdebug-3.0.2-7.4-vc15-x86_64.dll
 2. Move the downloaded file to C:\xampp\bin\php\php-7.4.12\ext
 3. Edit C:\xampp\bin\php\php-7.4.12\php.ini and add the line
zend_extension = C:\xampp\bin\php\php-7.4.12\ext\php_xdebug-3.0.2-7.4-vc15-x86_64.dll

### Cấu hình XDebug trong VSCode
Cách tích hợp XDebug trong VSCode cũng rất đơn giản, chúng ta chỉ cần tìm kiếm tiện ích **PHP Debug** rồi cài đặt. Sau đó mở file cấu hình **php.ini** và thêm vào đoạn sau:
```
xdebug.mode = debug
xdebug.start_with_request = yes
xdebug.client_port = 9000
```
Tiếp theo, mở tab **Debug** ở thanh bên trái của VSCode và ấn vào menu Debug rồi chọn **Add Configuration**. Một popup sẽ hiện ra, chọn tiếp PHP và một file *launch.json* sẽ được tạo ra với nội dung như sau:
~~~json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Listen for XDebug",
      "type": "php",
      "request": "launch",
      "port": 9000
    },
    {
      "name": "Launch currently open script",
      "type": "php",
      "request": "launch",
      "program": "${file}",
      "cwd": "${fileDirname}",
      "port": 9000
    }
  ]
}
~~~
Vậy là chúng ta đã hoàn thành việc cấu hình XDebug trong VSCode cho project hiện tại. Để có thể sử dụng XDebug ở bất cứ project nào, chỉ cần copy nội dung của file *launch.json* và paste vào phần **launch** trong file *settings.json* của VSCode. Ngoài ra các bạn có thể tham khảo thêm toàn bộ cách sử dụng của XDebug tại [đây](https://xdebug.org/docs/), để có thể debug ứng dụng một cách dễ dàng nhất.