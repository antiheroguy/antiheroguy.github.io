---
layout: post
title:  "Sử dụng local package với Composer"
---

[Composer](https://getcomposer.org/) - công cụ tuyệt vời không chỉ giúp cho việc tương tác với các dependency trở nên dễ dàng hơn mà còn là cầu nối để developer có thể chia sẻ mã nguồn các package của mình với cộng động.

Không ít người với mục đích học tập, nghiên cứu đã tự xây dựng nên những package của riêng mình. Tuy nhiên trong quá trình phát triển, họ muốn được thử nghiệm, kiểm tra package của mình rồi mới publish lên cộng đồng. Hay đơn giản họ chỉ muốn sử dụng các package đó cho mục đích cá nhân thay vì công khai. Vậy phải làm như thế nào?

Giải pháp ở đây đó là chúng ta sẽ require package từ một thư mục bất kì trên máy tính và sử dụng như một **local repository**

## Show me the code
Cách thức tiến hành rất đơn giản, chúng ta chỉ cần thêm phần định nghĩa **repositories** trong file `composer.json`:
~~~json
"repositories":[
    {
        "type": "path",
        "url": "/path/to/your/local/package",
        "options": {
            "symlink": true
        }
    }
]
~~~
Bằng cách chỉ định `"type": "path"` **Composer** sẽ biết được rằng bạn muốn include một **local repository** có chứa package của bạn với `url` là path của thư mục chứa package của bạn. Các bạn có thể đọc thêm về **Composer Repositories** ở [đây](https://getcomposer.org/doc/05-repositories.md#path)

Việc tiếp theo là require package của bạn:
~~~bash
composer require YOUR_PACKAGE @dev
~~~
Bạn cũng nên chỉ định `"minimum-stability": "dev"` trong file **composer.json** để có thể require package dưới dạng **devDependencies**

**Composer** sẽ quét thư mục mà bạn cung cấp ở phần `url` và nếu phát hiện package thích hợp, nó sẽ được include vào **composer.json**. Đồng thời bên trong thư mục **vendor** cũng sẽ xuất hiện package của bạn. Điều đặc biệt là mỗi khi bạn chỉnh sửa gì đó trong package thì phần code tương ứng sẽ được tự động cập nhật vào thư mục vendor. Có được điều đó là nhờ vào option `"symlink": true` mà chúng ta đã khai báo ở trên.

Giờ thì bạn có thể thoải mái điều chỉnh package của mình rồi. Sau khi đã "ngắm nghía" chán chê và publish package lên cộng đồng, nếu bạn muốn require package một cách trực tiếp từ packagist thì hãy clear cache của composer đi nhé. Để tránh việc composer sẽ require package ở local của bạn thay vì từ packagist:
~~~bash
composer clearcache
~~~