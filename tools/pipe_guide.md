# Hướng dẫn chi tiết cài đặt và chạy dự án Piper TTS

Đây là hướng dẫn toàn diện để cài đặt và chạy dự án chuyển văn bản thành giọng nói (Text-to-Speech) Piper từ mã nguồn trên hệ thống của bạn.

## Yêu cầu tiên quyết

Trước khi bắt đầu, hãy đảm bảo bạn đã cài đặt các công cụ sau trên hệ thống của mình:

*   **Conda**: Một hệ thống quản lý gói và môi trường. Bạn có thể cài đặt [Miniconda](https://docs.conda.io/en/latest/miniconda.html) (khuyến nghị) hoặc [Anaconda](https://www.anaconda.com/products/individual).

## Tổng hợp các lệnh cài đặt

Dưới đây là toàn bộ các lệnh cần thiết để cài đặt dự án từ đầu. Bạn có thể chạy từng lệnh một.

```bash
# Bước 1: Tạo một môi trường Conda mới với Python 3.11
source ~/miniconda3/etc/profile.d/conda.sh && conda create -n piper-env python=3.11 -y

# Bước 2: Kích hoạt môi trường vừa tạo
source ~/miniconda3/etc/profile.d/conda.sh && conda activate piper-env

# Bước 3: Cài đặt dự án Piper TTS và các thư viện phụ thuộc
# Lệnh này sẽ biên dịch các thành phần C/C++ cần thiết.
pip install .

# Bước 4: Tải xuống một giọng nói ví dụ (en_US-lessac-medium)
python3 -m piper.download_voices en_US-lessac-medium

# Bước 5: Chạy thử nghiệm để xác nhận cài đặt thành công
python3 -m piper -m en_US-lessac-medium -f test_output.wav -- 'Piper has been installed successfully.'

# (Tùy chọn) Kiểm tra xem tệp âm thanh đã được tạo chưa
ls -l test_output.wav
```

---

## Giải thích chi tiết các bước

### 1. Tạo và kích hoạt môi trường Conda

Dự án Piper TTS yêu cầu các phiên bản thư viện cụ thể để hoạt động ổn định. Để tránh xung đột với các dự án Python khác trên máy của bạn, chúng ta nên tạo một môi trường riêng biệt.

*   **Lệnh tạo môi trường**:
    ```bash
    source ~/miniconda3/etc/profile.d/conda.sh && conda create -n piper-env python=3.11 -y
    ```
    *   `source ~/miniconda3/etc/profile.d/conda.sh`: Lệnh này khởi tạo Conda trong phiên terminal hiện tại. Nếu bạn cài đặt Conda ở một vị trí khác, hãy thay đổi đường dẫn này.
    *   `conda create -n piper-env python=3.11 -y`: Lệnh này tạo một môi trường mới tên là `piper-env` sử dụng Python phiên bản 3.11. Phiên bản này được chọn vì nó tương thích với tất cả các thư viện phụ thuộc của dự án, đặc biệt là `onnxruntime`.

*   **Lệnh kích hoạt môi trường**:
    ```bash
    source ~/miniconda3/etc/profile.d/conda.sh && conda activate piper-env
    ```
    Sau khi chạy lệnh này, tất cả các lệnh `python` và `pip` tiếp theo sẽ được thực thi bên trong môi trường `piper-env` biệt lập này.

### 2. Cài đặt dự án Piper TTS

Quá trình này cài đặt gói `piper-tts` từ mã nguồn vào môi trường `piper-env` của bạn.

*   **Lệnh cài đặt**:
    ```bash
    pip install .
    ```
    *   Lệnh này đọc các tệp `pyproject.toml` và `setup.py` trong thư mục dự án.
    *   Nó sẽ tự động tải xuống và cài đặt tất cả các thư viện Python được định nghĩa trong `install_requires` (như `onnxruntime`).
    *   Quan trọng nhất, nó sẽ sử dụng `scikit-build` và `cmake` để biên dịch mã C/C++ (cụ thể là `espeakbridge.c`) thành một mô-đun Python có thể sử dụng được. Đây là bước quan trọng để chức năng phonemization (phân tích âm vị) hoạt động.
    *   Chúng ta sử dụng `pip install .` thay vì `pip install -e .` (chế độ chỉnh sửa) để đảm bảo quá trình biên dịch và đóng gói được thực hiện đầy đủ, tạo ra một bản cài đặt ổn định.

### 3. Tải xuống mô hình giọng nói

Piper cần các tệp mô hình giọng nói đã được huấn luyện để tạo ra âm thanh.

*   **Lệnh tải xuống giọng nói**:
    ```bash
    python3 -m piper.download_voices en_US-lessac-medium
    ```
    Lệnh này chạy mô-đun `piper.download_voices` để tải xuống giọng `en_US-lessac-medium` từ kho lưu trữ trực tuyến. Các tệp mô hình (`.onnx` và `.json`) sẽ được lưu vào thư mục hiện tại.

### 4. Chạy và kiểm tra

Sau khi cài đặt xong, bạn nên chạy thử để đảm bảo mọi thứ hoạt động chính xác.

*   **Lệnh chạy thử nghiệm**:
    ```bash
    python3 -m piper -m en_US-lessac-medium -f test_output.wav -- 'Piper has been installed successfully.'
    ```
    *   `python3 -m piper`: Chạy mô-đun Piper dưới dạng một script.
    *   `-m en_US-lessac-medium`: Chỉ định mô hình giọng nói sẽ sử dụng.
    *   `-f test_output.wav`: Chỉ định tệp âm thanh đầu ra.
    *   `-- '...'`: Cung cấp văn bản đầu vào để chuyển đổi.

Nếu lệnh này chạy mà không có lỗi và tạo ra tệp `test_output.wav`, điều đó có nghĩa là bạn đã cài đặt thành công dự án.
