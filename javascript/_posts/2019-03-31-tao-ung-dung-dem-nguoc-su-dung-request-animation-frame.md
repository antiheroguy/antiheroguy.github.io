---
layout: post
title:  "Tạo ứng dụng đếm ngược sử dụng requestAnimationFrame"
---

Khi phát triển các ứng dụng web, đôi khi bạn cần một đồng hồ đếm ngược mà không bị ảnh hưởng bởi việc người dùng chuyển giữa các tab. Để đạt được điều này, bạn có thể sử dụng `requestAnimationFrame` kết hợp với sự kiện `visibilitychange`. Trong bài viết này, chúng ta sẽ tạo một ứng dụng đồng hồ đếm ngược đơn giản mà sẽ dừng lại khi người dùng rời khỏi tab và tiếp tục khi họ quay lại.

## Code HTML chi tiết

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tab Active Timer with requestAnimationFrame</title>
</head>
<body>
  <h1>Tab Active Timer</h1>
  <p id="timer">Time left: 60 seconds</p>

  <script>
    const countdownDuration = 60 * 1000;

    let startTime = 0;
    let elapsedTime = 0;
    let lastTimestamp = 0;
    let isActive = true;
    const timerElement = document.getElementById('timer');

    function updateTimer(timestamp) {
      if (isActive) {
        if (lastTimestamp) {
          elapsedTime += timestamp - lastTimestamp;
        }
        lastTimestamp = timestamp;

        const remainingTime = countdownDuration - elapsedTime;
        if (remainingTime <= 0) {
          timerElement.textContent = 'Time is up!';
          return;
        }

        const seconds = Math.ceil(remainingTime / 1000);
        timerElement.textContent = `Time left: ${seconds} seconds`;

        requestAnimationFrame(updateTimer);
      }
    }

    function startTimer() {
      isActive = true;
      lastTimestamp = performance.now();
      requestAnimationFrame(updateTimer);
    }

    function stopTimer() {
      isActive = false;
    }

    document.addEventListener('visibilitychange', () => {
      if (document.visibilityState === 'visible') {
        startTimer();
      } else {
        stopTimer();
      }
    });

    startTimer();
  </script>
</body>
</html>
~~~

## Giải Thích

1. **Cài Đặt Thời Gian Đếm Ngược:**
   - Biến `countdownDuration` được thiết lập cho thời gian đếm ngược (60 giây trong ví dụ này).

2. **Hàm `updateTimer`:**
   - Hàm này được gọi liên tục bởi `requestAnimationFrame`. Nó tính toán thời gian còn lại và cập nhật nội dung của phần tử hiển thị đồng hồ.

3. **Bắt Đầu và Dừng Đồng Hồ:**
   - `startTimer` bắt đầu đếm ngược và `stopTimer` dừng đếm ngược khi người dùng chuyển tab.

4. **Sự Kiện `visibilitychange`:**
   - Khi người dùng chuyển tab, đồng hồ sẽ dừng lại. Khi người dùng quay lại, đồng hồ sẽ tiếp tục đếm ngược từ thời điểm trước khi chuyển tab.

Với đoạn mã này, bạn có thể tạo một đồng hồ đếm ngược hoạt động chính xác ngay cả khi người dùng chuyển đổi giữa các tab trong trình duyệt.
