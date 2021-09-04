---
layout: post
title: "Tự động format code PHP với PHP-CS-Fixer"
---

Format code một cách nhất quán là một vấn đề khó khăn, đặc biệt là khi làm việc theo nhóm. Trong bài viết này, chúng ta sẽ cùng nhau tìm hiểu cách cấu hình tự động format code PHP với tiện ích **PHP CS Fixer**.

## Show me the code

### Bước 1: Cài đặt PHP CS Fixer
PHP Coding Standards Fixer (PHP CS Fixer) là một công cụ dùng để chuẩn hóa code PHP theo các quy chuẩn được định nghĩa trước. Đó có thể là PSR-1, PSR-2, v.v. hay một format nào đó do chính chúng ta định nghĩa. PHP CS Fixer được cài đặt qua [Composer](https://getcomposer.org/) bằng câu lệnh:
~~~shell
composer global require friendsofphp/php-cs-fixer
~~~
Chúng ta nên cài đặt PHP CS Fixer ở mức global để có thể sử dụng được trong bất cứ  dự án PHP nào.

### Bước 2: Thêm PHP CS Fixer vào biến môi trường
Việc cài đặt PHP CS Fixer bằng Composer sẽ tạo ra một file **php-cs-fixer.bat** trong thư mục bin của Composer. Tương tự như những command line tool được cài đặt thông qua Composer khác, điều bạn cần làm là thêm đường dẫn của thư mục **vendor/bin** trong Composer vào **environment variable** (biến môi trường), để có thể thực thi được giao diện dòng lệnh của tool. Với người dùng Windows, có thể mở start menu và tìm kiếm **environment variables**. Chỉnh sửa phần **PATH** và thêm vào dòng dưới đây:
```
%USERPROFILE%\AppData\Roaming\Composer\vendor\bin
```
Vậy là từ giờ chúng ta đã có thể fix code PHP bằng cách mở **cmd** và gõ lệnh:
~~~shell
php-cs-fixer fix /path/to/dir
php-cs-fixer fix /path/to/file
~~~
Để có thể fix code cho một file/folder cụ thể. Hoặc đơn giản:
~~~shell
php-cs-fixer fix
~~~
Để fix code trong toàn bộ dự án.

### Bước 3: Cấu hình PHP CS Fixer
Chúng ta có thể định nghĩa các **Rule**, để PHP CS Fixer dựa vào đó mà format code theo ý muốn của chúng ta. Ví dụ:
~~~shell
php-cs-fixer describe single_quote
~~~
Rule **single_quote** cho phép chúng ta chuyển đổi tất cả những đoạn string trong code từ nháy kép sang nháy đơn.
Danh sách các Rule có thể tham khảo chi tiết ở [đây](https://github.com/FriendsOfPHP/PHP-CS-Fixer#rules)
Sau khi đã định nghĩa được những Rule mà mình cần, chúng ta có thể lưu lại cấu hình bằng cách tạo một file **.php-cs-fixer.php** ở thư mục root của dự án. Điều này giúp chúng ta có thể định nghĩa được format code theo từng dự án. Mặc định mỗi khi chạy PHP CS Fixer, một file **.php-cs-fixer.cache** sẽ được sinh ra ở cùng cấp với file **.php-cs-fixer.php** để cache lại quá trình fix code. Điều này sẽ tăng tốc độ fix code bằng việc chỉ fix các file đã được sửa đổi kể từ lần chạy cuối cùng. Hãy thêm **.php-cs-fixer.cache** vào **.gitignore** để tránh việc conflict trong quá trình fix code giữa các thiết bị khác nhau.

### Bước 4: Thiết lập tự động format code với VS Code
VS Code không chỉ là một text editor tuyệt vời mà nó còn hỗ trợ vô vàn những tiện ích phục vụ cho nhiều mục đích khác nhau. Cụ thể, chúng ta có thể sử dụng tiện ích **php cs fixer** để thiết lập tự động format mỗi khi save file. Đầu tiên chúng ta cần tải tiện ích **php cs fixer** tại [đây](https://marketplace.visualstudio.com/items?itemName=junstyle.php-cs-fixer)
Sau khi cài đặt thành công, hãy mở file **setting** của VS Code và thêm đoạn cấu hình sau:
~~~json
{
	"php-cs-fixer.executablePath": "${extensionPath}\\php-cs-fixer.phar",
	"php-cs-fixer.executablePathWindows": "C:\\Users\\{USER_NAME}\\AppData\\Roaming\\Composer\\vendor\\bin\\php-cs-fixer.bat",
	"php-cs-fixer.onsave": true,
	"php-cs-fixer.autoFixBySemicolon": true,
	"php-cs-fixer.formatHtml": true,
}
~~~
Trong đó **USER_NAME** là thư mục người dùng của bạn.
Done, vậy là từ giờ code PHP của bạn sẽ được tự động format mỗi khi save file. Rất tuyệt vời phải không nào?