# Tạo key va liên kết SSH key trên github

##  1.Kiểm tra và cài đặt Git (nếu chưa có)

git --version

## 2.Tạo SSH key 

### 2.1 Kiểm tra SSH key hiện có

#### ls -al ~/.ssh 

### 2.2 Tạo SSH key mớ

ssh-keygen -t ed25519 -C "luonghailam@gmail.com"

### 2.3 Thêm SSH private key vào ssh-agent

Khởi động ssh-agent:

#### eval "$(ssh-agent -s)"

Thêm private key vào ssh-agent:

#### ssh-add ~/.ssh/id_ed25519

# 3. Thêm SSH Public Key vào GitHub

### 3.1 Sao chép public Key

Sao chép nội dung của public key vào clipboard:

#### cat ~/.ssh/id_ed25519.pub

### 3.2 Thêm key vào GitHub

> Đăng nhập vào GitHub (truy cập https://github.com).

>Vào Settings (nhấn vào ảnh đại diện ở góc trên bên phải).

>Chọn SSH and GPG keys từ menu bên trái.

>Nhấn New SSH key hoặc Add SSH key.

>Đặt tên cho key (ví dụ: "Ubuntu Laptop").

>Dán nội dung public key vào ô Key.

>Nhấn Add SSH key và xác nhận mật khẩu GitHub nếu được yêu cầu.



## 4. Kiểm tra kết nối SSH với github

Kiểm tra xem SSH đã hoạt động đúng chưa:

#### ssh -T git@github.com

Nếu thành công, bạn sẽ thấy thông báo giống như:

Hi HaiLammm! You've successfully authenticated, but GitHub does not provide shell access.
