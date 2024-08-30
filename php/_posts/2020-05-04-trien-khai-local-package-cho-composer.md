---
layout: post
title:  "Triển khai Local Package cho Composer"
---

Khi phát triển một package PHP, việc kiểm tra và chỉnh sửa thường xuyên là điều không thể tránh khỏi. Để tiện cho quá trình phát triển, bạn có thể sử dụng Composer để triển khai package cục bộ (local package). Điều này giúp bạn có thể sửa đổi code trong thư mục chứa package và thấy ngay những thay đổi trong dự án sử dụng package đó mà không cần phải update thủ công trong `vendor`. Dưới đây là hướng dẫn chi tiết.

# Show me the code

## Chuẩn Bị Cấu Trúc Thư Mục

Giả sử bạn có cấu trúc thư mục như sau:

~~~
/my-project
  /src
  /vendor
  /packages
    /my-local-package
      composer.json
~~~

- **`my-project`**: Thư mục dự án chính của bạn.
- **`packages/my-local-package`**: Thư mục chứa package cục bộ của bạn.

## Tạo File `composer.json` Cho Package Cục Bộ

Trong thư mục `my-local-package`, bạn tạo file `composer.json` với nội dung cơ bản như sau:

~~~json
{
    "name": "my-vendor/my-local-package",
    "description": "My local package for development",
    "type": "library",
    "autoload": {
        "psr-4": {
            "MyVendor\\MyLocalPackage\\": "src/"
        }
    },
    "require": {}
}
~~~

- **`name`**: Đặt tên cho package theo định dạng `vendor/package-name`.
- **`autoload`**: Khai báo cách tự động load các class từ package.

## Khai Báo Package Cục Bộ Trong Dự Án Chính

Trong dự án chính (`my-project`), bạn mở file `composer.json` và thêm đường dẫn đến package cục bộ trong phần `repositories` và sau đó yêu cầu package này:

~~~json
{
    "repositories": [
        {
            "type": "path",
            "url": "./packages/my-local-package"
        }
    ],
    "require": {
        "my-vendor/my-local-package": "*"
    }
}
~~~

- **`repositories`**: Định nghĩa repository của package cục bộ. `type` là `path`, và `url` là đường dẫn đến package.
- **`require`**: Yêu cầu package này trong dự án của bạn.

## Cài Đặt Package

Chạy lệnh sau trong terminal để cài đặt package:

~~~bash
composer update
~~~

Lệnh này sẽ cài đặt package từ đường dẫn cục bộ mà bạn đã chỉ định.

## Sử Dụng Chế Độ `symlink` Để Phát Triển Package

Để tiện lợi trong quá trình phát triển, Composer sẽ tạo một symbolic link (symlink) đến thư mục package thay vì sao chép toàn bộ code vào `vendor`. Điều này giúp mọi thay đổi bạn thực hiện trong `packages/my-local-package` sẽ ngay lập tức có hiệu lực trong dự án của bạn.

Bạn chỉ cần chắc chắn rằng phần cấu hình `composer.json` của package cục bộ có dạng:

~~~json
{
    "name": "my-vendor/my-local-package",
    "version": "1.0.0",
    "type": "library",
    "autoload": {
        "psr-4": {
            "MyVendor\\MyLocalPackage\\": "src/"
        }
    },
    "extra": {
        "branch-alias": {
            "dev-main": "1.0.x-dev"
        }
    }
}
~~~

## Kiểm Tra và Phát Triển

Bây giờ, khi bạn thay đổi code trong thư mục `packages/my-local-package`, bạn không cần làm gì thêm; mọi thay đổi sẽ tự động có hiệu lực trong dự án của bạn vì Composer đã thiết lập liên kết động.

Với phương pháp này, bạn có thể phát triển và kiểm tra package một cách nhanh chóng và dễ dàng. Sau khi hoàn thiện, bạn có thể public package này lên Packagist hoặc một kho lưu trữ khác mà không cần thay đổi nhiều. Việc sử dụng `symlink` giúp bạn giảm bớt thời gian và công sức trong việc phát triển package cục bộ.
