---
layout: post
title:  "Các extension hay cho Visual Studio Code"
---

Visual Studio Code (VSCode) đã trở thành một trong những trình soạn thảo mã nguồn phổ biến nhất cho các nhà phát triển. Một trong những lợi ích lớn của VSCode đó là khả năng mở rộng sự tích hợp thông qua các extension. Hôm nay, chúng ta sẽ khám phá một số extension hữu ích mà bạn có thể cài đặt để nâng cao trải nghiệm làm việc với VSCode.

## Những Extension VS Code hữu ích giúp cải thiện trải nghiệm lập trình

### [Bookmarks](https://marketplace.visualstudio.com/items?itemName=alefragnani.bookmarks) (alefragnani.bookmarks)

* Cho phép bạn đánh dấu các dòng code quan trọng và chuyển nhanh giữa các điểm đánh dấu trong file hoặc giữa nhiều file. Rất hữu ích khi bạn làm việc với file dài.

### [Change Case](https://marketplace.visualstudio.com/items?itemName=wmaurer.change-case) (wmaurer.change-case)

* Dễ dàng chuyển đổi chuỗi giữa các định dạng camelCase, snake\_case, kebab-case, PascalCase,... chỉ bằng vài phím bấm.

### [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) (streetsidesoftware.code-spell-checker)

* Tự động kiểm tra chính tả trong comment, chuỗi văn bản, tài liệu Markdown,... giúp bạn tránh những lỗi chính tả nhỏ nhặt nhưng đáng tiếc.

### [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-containers) (ms-azuretools.vscode-containers)

* Hỗ trợ quản lý Docker container, image, volume ngay trong VS Code. Có thể xem logs, mở terminal, attach vào container rất thuận tiện.

### [Fluent Icons](https://marketplace.visualstudio.com/items?itemName=miguelsolorio.fluent-icons) (miguelsolorio.fluent-icons)

* Bộ icon theo phong cách Fluent Design hiện đại, tạo cảm giác dễ chịu và mới mẻ cho sidebar và file explorer của VS Code.

### [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) (eamodio.gitlens)

* Mở rộng chức năng Git trong VS Code: xem lịch sử commit theo dòng, blame rõ ràng từng thay đổi, hiển thị tác giả, thời gian chỉnh sửa... cực kỳ hữu dụng khi làm việc nhóm.

### [Increment Selection](https://marketplace.visualstudio.com/items?itemName=albymor.increment-selection) (albymor.increment-selection)

* Cho phép bạn mở rộng vùng chọn một cách thông minh theo cấu trúc mã nguồn, không chỉ đơn thuần là chọn từ hoặc dòng như mặc định.

### [Indent Rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow) (oderwat.indent-rainbow)

* Tô màu cho các mức thụt dòng bằng màu sắc khác nhau, giúp dễ phân biệt các cấp độ lồng nhau trong code, đặc biệt hữu ích với Python hoặc YAML.

### [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) (ritwickdey.liveserver)

* Mở một server cục bộ và reload trang tự động khi bạn chỉnh sửa file HTML, CSS, JS. Phù hợp khi làm web tĩnh hoặc thử nghiệm nhanh giao diện.

### [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=pkief.material-icon-theme) (pkief.material-icon-theme)

* Thay đổi icon file/folder theo Material Design. Hỗ trợ nhiều định dạng file và framework như Angular, Vue, Laravel, Docker,...

### [One Dark Pro (Material Theme)](https://marketplace.visualstudio.com/items?itemName=zhuangtongfa.Material-theme) (zhuangtongfa.material-theme)

* Một trong những theme phổ biến nhất cho VS Code, lấy cảm hứng từ Atom's One Dark với màu sắc hài hòa, dễ chịu cho mắt khi làm việc lâu.

### [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) (ms-vscode-remote.remote-wsl)

* Giúp bạn mở và phát triển dự án trong môi trường WSL (Windows Subsystem for Linux) ngay trong VS Code, như thể bạn đang làm việc trên Linux thực thụ.

### [Run on Save](https://marketplace.visualstudio.com/items?itemName=emeraldwalk.RunOnSave) (emeraldwalk.runonsave)

* Tự động chạy một hoặc nhiều lệnh terminal khi bạn lưu file, chẳng hạn như `eslint`, `prettier`, `php artisan`,... Rất hữu ích cho CI/CD nhẹ tại local.

### [Sort Lines](https://marketplace.visualstudio.com/items?itemName=tyriar.sort-lines) (tyriar.sort-lines)

* Sắp xếp các dòng văn bản theo thứ tự alphabet (tăng hoặc giảm). Cực kỳ tiện lợi khi bạn xử lý danh sách, config, enum,...

### [Sublime Keymap](https://marketplace.visualstudio.com/items?itemName=ms-vscode.sublime-keybindings) (ms-vscode.sublime-keybindings)

* Mang toàn bộ keybinding của Sublime Text sang VS Code. Nếu bạn là fan của Sublime, extension này sẽ giúp bạn cảm thấy quen thuộc ngay lập tức.

## Xuất & nhập danh sách extensions giữa các máy

Nếu bạn muốn **chuyển extension từ máy A sang máy B**, hãy làm như sau:

### Xuất danh sách extension:

~~~bash
code --list-extensions > extensions.txt
~~~

### Import vào máy khác:

~~~bash
cat extensions.txt | xargs -n 1 code --install-extension
~~~

💡 Với PowerShell (trên Windows), bạn dùng:

~~~bash
Get-Content extensions.txt | ForEach-Object { code --install-extension $_ }
~~~

Với sự đa dạng của các extension này, bạn có thể tùy chỉnh VSCode theo cách tốt nhất phù hợp với nhu cầu phát triển của mình. Hãy tải xuống và thử nghiệm để tận dụng sức mạnh của trình soạn thảo mã nguồn này.
