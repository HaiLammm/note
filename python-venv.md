# 1.Cài đặt Python3 và pip
### sudo apt install -y python3 python3-pip
## 1.1 Kiểm tra phiên bản
### python3 --version
### pip3 --version
# 2.Tạo môi trường ảo và kích hoạt
## 2.1 Tạo môi trường ảo
### python3 -m venv venv
## 2.2 Kích hoạt môi trường ảo
Sử dụng Linux
### source venv/bin/activate
Sử dụng Windown
### venv\Scripts\activate.bat
# 3. Tạo requirements.txt
## 3.1 Tạo requirements.txt
### pip freeze > requirements.txt
## 3.2 Sử dụng requirements.txt
Tệp requirements.txt sẽ chứa danh sách các thư viện và phiên bản tương ứng.
Để cài đặt các thư viện từ requirements.txt trong một môi trường ảo khác:
### source venv/bin/activate
### pip install -r requirements.txt
Để cập nhật requirements.txt sau khi cài thêm thư viện:
### pip freeze > requirements.txt



