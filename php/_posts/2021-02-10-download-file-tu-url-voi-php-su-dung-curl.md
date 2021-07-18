---
layout: post
title: "Download file từ URL với PHP sử dụng CURL"
---

Để download một file từ URL với PHP, cách đầu tiên chúng ta nghĩ đến thường là sử dụng hàm `file_get_contents`. Tuy nhiên có một cách khác cũng lợi hại không kém, đó là sử dụng **CURL** - một khái niệm phổ biến và quen thuộc với PHP Developer. Trong bài viết này, chúng ta không tìm hiểu xem CURL là gì mà chỉ tập trung vào việc sử dụng CURL để download một file từ URL.

## Show me the code
Nếu là fan ruột của bộ truyện tranh nổi tiếng **One Piece**, hẳn bạn đã từng nghe đến tựa game **One Piece Treasure Cruise**. Không bàn đến nội dung của game, chúng ta sẽ chỉ quan tâm đến thư viện ảnh của nó: Một bộ sưu tập tuyệt đẹp và vô cùng đồ sộ của những nhân vật trong game. Không khó để tìm được [trang chủ](http://onepiece-treasurecruise.com/) của game, và nhiệm vụ của chúng ta là tải tất cả bộ sưu tập này về máy.

Để ý một chút sẽ thấy website này sử dụng Wordpress, và link ảnh có dạng chung http://onepiece-treasurecruise.com/wp-content/uploads/cXXXX.png, trong đó XXXX là số thứ tự của nhân vật. Như vậy chúng ta sẽ xây dựng một hàm cho phép tải về từ ảnh đầu tiên cho đến ảnh mới nhất của bộ sưu tập.

~~~php
$savePath = 'PATH_TO_SAVE';
$lastNumber = 3200;

for ($i = 1; $i <= $lastNumber; $i++)
{
    $name = str_pad($i, 4, '0', STR_PAD_LEFT);
    $path = $savePath . '/' . $i . '.png';
    if (file_exists($path)) {
        continue;
    }

    $fp = fopen($path, 'w+');

    $ch = curl_init('http://onepiece-treasurecruise.com/en/wp-content/uploads/c' . $name . '.png');
    curl_setopt_array($ch, [
        CURLOPT_TIMEOUT => 10, 
        CURLOPT_FILE => $fp, 
    ]);
    curl_exec($ch);
    curl_close($ch);

    fclose($fp);
}
~~~

Hàm này không có gì đặc biệt, `$savePath` là thư mục sẽ lưu file download, `$lastNumber` là số thứ tự của ảnh mới nhất, hàm `str_pad` để thêm các chữ số 0 vào đầu sao cho tên của ảnh đang lặp ở dạng số có 4 chữ số. Điều chúng ta cần quan tâm đó là option `CURLOPT_FILE` của CURL.

`CURLOPT_FILE` nhận vào giá trị là một stream (ví dụ: sử dụng `fopen`). Option này sẽ chỉ định nơi mà file nhận về sẽ được lưu vào. Vậy là xong, giờ bạn đã có trong tay bộ sưu tập các nhận vật của **One Piece Treasure Cruise**. Sử dụng CURL để download file thật đơn giản phải không nào.