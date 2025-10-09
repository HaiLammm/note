# Note Project

Thư viện tài liệu hướng dẫn và ghi chú về môi trường phát triển, cài đặt phần mềm, và các công cụ lập trình.

## Cấu trúc thư mục

### 📁 `/github/`
- **ssh.md** - Hướng dẫn tạo và quản lý SSH key cho GitHub
  - Cài đặt Git
  - Tạo SSH key
  - Thêm SSH key vào GitHub
  - Kiểm tra kết nối SSH

### 📁 `/install/`
- **alacritty.md** - Hướng dẫn cài đặt và cấu hình Alacritty trên Linux Mint
  - Cài đặt JetBrains Mono Nerd Font
  - Cài đặt Alacritty qua PPA
  - Cấu hình font và giao diện
  - Sử dụng GNU Stow để quản lý cấu hình

### 📁 `/python/`
- **python-venv.md** - Hướng dẫn sử dụng Python 3 và môi trường ảo
  - Cài đặt Python 3 và pip
  - Tạo và kích hoạt môi trường ảo
  - Quản lý dependencies với requirements.txt

- **zed-ide-ai.md** - Hướng dẫn cài đặt Zed IDE trên Linux Mint
  - Yêu cầu hệ thống
  - Cài đặt Zed
  - Chạy Zed IDE

- **discord/discord.md** - Kiến trúc chatbot Discord chuẩn
  - Tổng quan 4 tầng chức năng
  - Cấu trúc thư mục chuẩn
  - Core Layer, Command Layer, Event Layer, Utility Layer

- **discord/chatbot.md** - (Nội dung chi tiết về Discord chatbot)

## 💡 Cách sử dụng

### 1. Tài liệu cài đặt
Mỗi thư mục chứa tài liệu hướng dẫn chi tiết cho từng công cụ hoặc ngôn ngữ. Bạn có thể:
- Đọc từng file `.md` để hiểu các bước cài đặt
- Sao chép các lệnh terminal để thực hiện
- Làm theo hướng dẫn từng bước

### 2. Git & GitHub
- Sử dụng tài liệu `/github/ssh.md` để thiết lập kết nối SSH với GitHub
- Hữu ích cho việc push code và quản lý repository từ xa

### 3. Môi trường Python
- Tham khảo `/python/python-venv.md` để thiết lập môi trường phát triển Python
- Tạo môi trường ảo để quản lý dependencies riêng biệt
- Sử dụng requirements.txt để reproduce môi trường

### 4. Công cụ开发
- **Alacritty**: Terminal emulator GPU-accelerated cho Linux
- **Zed IDE**: IDE hiện đại với hỗ trợ AI cho lập trình
- **Discord Bot**: Xây dựng chatbot với kiến trúc chuẩn

## 🚀 Các bước sử dụng chung

### Khởi đầu
1. Cài đặt các công cụ cần thiết: Git, Python, IDE
2. Thiết lập GitHub SSH key
3. Cấu hình môi trường phát triển (Python venv, terminal)

### Lập trình
1. Sử dụng Zed IDE cho trải nghiệm lập trình hiện đại
2. Đảm bảo sử dụng môi trường ảo Python cho từng dự án
3. Đẩy code lên GitHub bằng SSH

## 📝 Lưu ý

- Tất cả tài liệu được viết bằng tiếng Việt
- Mỗi tài liệu có thể sử dụng độc lập
- Một số tài liệu yêu cầu cài đặt các dependency trước
- Luôn kiểm tra phiên bản phần mềm phù hợp với hệ thống của bạn

## 🔧 Hỗ trợ

Nếu gặp vấn đề khi thực hiện theo hướng dẫn:
- Kiểm tra lại phiên bản hệ thống
- Đảm bảo follow đúng từng bước
- Xem xét cài đặt các dependency cần thiết

---

**Last updated**: October 2025
**Maintained by**: Your Name