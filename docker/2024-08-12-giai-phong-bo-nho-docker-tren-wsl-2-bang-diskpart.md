---
layout: post
title: "Giải Phóng Bộ Nhớ Docker Trên WSL 2 Bằng Diskpart"
---

Khi sử dụng Docker trên Windows với WSL 2 (Windows Subsystem for Linux), bạn có thể nhận thấy rằng dung lượng ổ đĩa của mình bị chiếm dụng đáng kể, đặc biệt nếu bạn sử dụng Docker trong thời gian dài. Điều này là do Docker lưu trữ các tệp dữ liệu và container trong một file hệ thống ảo có tên là VHDX. Qua thời gian, dung lượng của file này có thể tăng lên đáng kể, nhưng dung lượng thực sự sử dụng có thể thấp hơn rất nhiều. Trong bài viết này, chúng ta sẽ tìm hiểu cách giải phóng dung lượng bằng công cụ `diskpart`.

## VHDX là gì?

VHDX (Virtual Hard Disk) là định dạng file hệ thống ảo được sử dụng rộng rãi trong các môi trường ảo hóa như Hyper-V của Microsoft. Nó mô phỏng một ổ đĩa vật lý và có thể chứa hệ điều hành, dữ liệu, và các ứng dụng. Trong trường hợp của WSL 2, VHDX lưu trữ toàn bộ hệ thống file của bản phân phối Linux bạn đang sử dụng (như Ubuntu, Debian, v.v.), bao gồm cả dữ liệu Docker.

Khi bạn sử dụng Docker trên WSL 2, dữ liệu của các container, image và volume đều được lưu trong file VHDX này. Mặc dù VHDX có khả năng tự động mở rộng dung lượng khi cần thiết, nhưng nó không tự động giảm kích thước khi dữ liệu bên trong bị xóa, dẫn đến việc file này có thể chiếm dụng rất nhiều dung lượng ổ đĩa không cần thiết.

Qua thời gian, khi bạn xóa bớt các container hoặc image trong Docker, dung lượng thật sự sử dụng bên trong file VHDX sẽ giảm xuống, nhưng kích thước của chính file VHDX vẫn không thay đổi. Điều này có nghĩa là dung lượng ổ đĩa của bạn vẫn bị chiếm dụng bởi VHDX, mặc dù không còn nhiều dữ liệu bên trong nó. Do đó, việc thu nhỏ (compact) file VHDX là cần thiết để giải phóng dung lượng không cần thiết này.

## Cách giải phóng bộ nhớ Docker trên WSL 2

Dưới đây là hướng dẫn chi tiết các bước sử dụng `diskpart` để thu nhỏ dung lượng của file VHDX trong WSL 2.

### Bước 1: Tắt WSL

Trước khi tiến hành bất kỳ thao tác nào với file VHDX, bạn cần đảm bảo rằng không có tiến trình nào của WSL đang chạy. Điều này đảm bảo rằng file VHDX không bị khóa bởi hệ thống. Bạn có thể tắt tất cả các phiên WSL đang chạy bằng lệnh sau:

~~~bash
wsl --shutdown
~~~

Lệnh này sẽ tắt hoàn toàn tất cả các bản phân phối Linux đang chạy trên WSL 2.

### Bước 2: Mở Diskpart

`Diskpart` là một công cụ dòng lệnh mạnh mẽ của Windows, cho phép quản lý các đĩa, phân vùng và file hệ thống ảo. Để bắt đầu, bạn cần mở Command Prompt hoặc PowerShell với quyền quản trị viên (Run as Administrator) và nhập lệnh sau để khởi động `diskpart`:

~~~bash
diskpart
~~~

Giao diện dòng lệnh của `diskpart` sẽ xuất hiện, sẵn sàng để bạn thao tác với các ổ đĩa và file hệ thống ảo.

### Bước 3: Chọn file VHDX

Tiếp theo, bạn cần chỉ định chính xác file VHDX mà WSL 2 đang sử dụng. Thay thế `{YourUser}` bằng tên người dùng của bạn để tìm đúng đường dẫn:

~~~bash
select vdisk file="C:\Users\{YourUser}\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\ext4.vhdx"
~~~

Lệnh này sẽ chọn file VHDX mà hệ thống WSL 2 đang sử dụng. Đường dẫn này có thể khác nhau nếu bạn đang sử dụng bản phân phối Linux khác, vì vậy hãy đảm bảo bạn đã nhập đúng đường dẫn.

### Bước 4: Gắn VHDX dưới chế độ chỉ đọc

Để đảm bảo an toàn, bạn nên gắn (attach) file VHDX dưới chế độ chỉ đọc. Điều này giúp ngăn chặn bất kỳ thay đổi nào không mong muốn có thể xảy ra trong quá trình thao tác:

~~~bash
attach vdisk readonly
~~~

Lệnh này sẽ gắn file VHDX vào hệ thống, nhưng chỉ cho phép đọc, không cho phép ghi dữ liệu.

### Bước 5: Thu nhỏ dung lượng của VHDX

Sau khi gắn file VHDX, bạn có thể sử dụng lệnh `compact vdisk` để thu nhỏ dung lượng của nó. Lệnh này sẽ loại bỏ không gian trống bên trong file VHDX và giảm kích thước của nó trên ổ đĩa vật lý:

~~~bash
compact vdisk
~~~

Quá trình này có thể mất vài phút tùy thuộc vào dung lượng trống cần thu nhỏ.

### Bước 6: Tháo VHDX

Khi quá trình thu nhỏ hoàn tất, bạn nên tháo (detach) file VHDX khỏi hệ thống:

~~~bash
detach vdisk
~~~

Lệnh này sẽ ngắt kết nối file VHDX khỏi hệ thống, hoàn tất quá trình giải phóng dung lượng.

#### Bước 7: Thoát diskpart

Cuối cùng, nhập lệnh sau để thoát khỏi `diskpart`:

~~~bash
exit
~~~

Giao diện dòng lệnh của `diskpart` sẽ đóng lại và bạn có thể quay trở lại với các tác vụ thông thường trên hệ thống.

Việc quản lý và tối ưu hóa dung lượng lưu trữ là rất quan trọng, đặc biệt khi bạn làm việc với Docker trên WSL 2. Bằng cách thực hiện các bước trên, bạn có thể dễ dàng giải phóng dung lượng ổ đĩa bị chiếm dụng không cần thiết bởi file VHDX của WSL 2. Điều này không chỉ giúp hệ thống của bạn hoạt động mượt mà hơn mà còn giúp bạn tiết kiệm không gian lưu trữ quý giá trên ổ đĩa của mình.