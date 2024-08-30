---
layout: post
title: "Tự động format code PHP với PHP-CS-Fixer"
---

Trong phát triển PHP, tuân thủ các chuẩn mã nguồn là vô cùng quan trọng để duy trì tính nhất quán và dễ bảo trì cho dự án. PHP-CS-Fixer là một công cụ mạnh mẽ giúp bạn tự động định dạng mã nguồn theo các quy tắc bạn thiết lập. Bài viết này sẽ hướng dẫn bạn cách cài đặt và cấu hình PHP-CS-Fixer trong VS Code thông qua Composer.

# Show me the code

## Cài Đặt PHP-CS-Fixer với Composer

Đầu tiên, chúng ta sẽ cài đặt PHP-CS-Fixer bằng Composer. Composer là một công cụ quản lý phụ thuộc phổ biến trong PHP, cho phép bạn dễ dàng cài đặt và quản lý các package trong dự án của mình.

**Bước 1:** Mở terminal và điều hướng đến thư mục dự án của bạn.

**Bước 2:** Chạy lệnh sau để cài đặt PHP-CS-Fixer:

~~~bash
composer require --dev friendsofphp/php-cs-fixer
~~~

Lệnh này sẽ cài đặt PHP-CS-Fixer dưới dạng một package phát triển (`--dev`), giúp bạn sử dụng công cụ này mà không làm ảnh hưởng đến môi trường sản xuất.

## Tạo File Cấu Hình Cho PHP-CS-Fixer

Để PHP-CS-Fixer hoạt động theo các quy tắc mà bạn mong muốn, bạn cần tạo một file cấu hình.

**Bước 1:** Tạo file `.php-cs-fixer.php` trong thư mục gốc của dự án.

**Bước 2:** Thêm nội dung sau vào file `.php-cs-fixer.php`:

~~~php
<?php

use PhpCsFixer\Config;
use PhpCsFixer\Finder;

$finder = Finder::create()
    ->in(__DIR__)
    ->exclude(['vendor', 'node_modules'])
    ->name('*.php');

return (new Config())
    ->setRules([
        '@PSR12' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => ['sort_algorithm' => 'alpha'],
        'no_unused_imports' => true,
        'single_quote' => true,
    ])
    ->setFinder($finder);
~~~

Trong file này, chúng ta thiết lập các quy tắc định dạng mã nguồn như sử dụng chuẩn PSR-12, cú pháp mảng ngắn, sắp xếp import theo thứ tự bảng chữ cái, loại bỏ import không sử dụng, và sử dụng dấu nháy đơn. Bạn có thể tham khảo thêm các quy tắc khác [tại đây](https://cs.symfony.com/doc/rules/index.html).

## Tích Hợp PHP-CS-Fixer với VS Code

Để tích hợp PHP-CS-Fixer vào VS Code, chúng ta sẽ sử dụng extension "PHP CS Fixer" và cấu hình nó để tự động định dạng mã nguồn khi lưu file.

**Bước 1:** Cài đặt extension "PHP CS Fixer" trong VS Code.

- Mở VS Code, đi đến phần Extensions (`Ctrl + Shift + X`).
- Tìm kiếm "PHP CS Fixer" và cài đặt extension từ junstyle.

**Bước 2:** Cấu hình extension "PHP CS Fixer".

Thay vì sửa file cấu hình `settings.json` toàn cục, chúng ta sẽ lưu cấu hình ở mức độ dự án để dễ dàng quản lý trong VCS.

- Mở VS Code, đi đến phần Explorer, và tạo thư mục `.vscode` trong thư mục gốc của dự án (nếu chưa có).
- Tạo file `settings.json` bên trong thư mục `.vscode` và thêm các cấu hình sau:

    ~~~json
    {
        "php-cs-fixer.executablePath": "./vendor/bin/php-cs-fixer",
        "php-cs-fixer.onsave": true,
        "php-cs-fixer.config": ".php-cs-fixer.php",
        "editor.formatOnSave": true,
        "[php]": {
            "editor.defaultFormatter": "junstyle.php-cs-fixer"
        }
    }
    ~~~

    - `"php-cs-fixer.executablePath"`: Chỉ định đường dẫn đến PHP-CS-Fixer trong thư mục `vendor` của dự án.
    - `"php-cs-fixer.onsave"`: Bật tính năng tự động fix code khi lưu file.
    - `"php-cs-fixer.config"`: Đặt đường dẫn tới file cấu hình `.php-cs-fixer.php`.
    - `"editor.formatOnSave"`: Bật tính năng tự động định dạng mã khi lưu file.
    - `"[php]"`: Đặt PHP-CS-Fixer làm trình định dạng mặc định cho các file PHP.

Việc lưu cấu hình tại mức độ dự án trong thư mục `.vscode` giúp bạn dễ dàng quản lý và chia sẻ các thiết lập này với team thông qua VCS, đảm bảo mọi người cùng sử dụng chung một cấu hình.

## Chạy PHP-CS-Fixer

Bạn có thể chạy PHP-CS-Fixer theo hai cách:

**Cách 1: Chạy từ terminal**

Để fix toàn bộ dự án:

~~~bash
./vendor/bin/php-cs-fixer fix
~~~

Để fix một file cụ thể:

~~~bash
./vendor/bin/php-cs-fixer fix path/to/your/file.php
~~~

**Cách 2: Sử dụng phím tắt trong VS Code**

- Mở file PHP mà bạn muốn fix.
- Nhấn `Ctrl + Shift + I` để định dạng file ngay lập tức theo các quy tắc đã cấu hình.

Với các bước trên, bạn đã cấu hình thành công PHP-CS-Fixer để tự động sửa lỗi mã nguồn trong dự án PHP của mình và tích hợp hoàn toàn với VS Code. Điều này giúp mã nguồn của bạn luôn tuân thủ các tiêu chuẩn và giảm thiểu thời gian sửa lỗi thủ công. Việc lưu cấu hình tại mức độ dự án giúp bạn dễ dàng chia sẻ và quản lý cấu hình trong đội ngũ.
