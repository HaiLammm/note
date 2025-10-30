## Hướng dẫn tải hình ảnh từ Images-url của file excel

#### Set up môi trường python

B1 : Tạo file main.py và dán script sau:

```python
import os
import requests
import openpyxl
from openpyxl.utils import column_index_from_string

# --- Cấu hình ---
EXCEL_FILE_NAME = 'store_scraper.grocery_stores_test1.xlsx'  # <-- Thay đổi tên file Excel của bạn ở đây
COLUMN_LETTER = 'C'            # <-- Cột chứa URL
DOWNLOAD_FOLDER = 'images/'
# ------------------

def get_file_extension(content_type):
    """Lấy đuôi file từ Content-Type header."""
    if 'image/jpeg' in content_type:
        return '.jpg'
    if 'image/png' in content_type:
        return '.png'
    if 'image/gif' in content_type:
        return '.gif'
    if 'image/webp' in content_type:
        return '.webp'
    return '.jpg'  # Mặc định là .jpg nếu không nhận diện được

def download_images_from_excel():
    # 1. Tạo thư mục nếu chưa tồn tại
    os.makedirs(DOWNLOAD_FOLDER, exist_ok=True)

    # 2. Mở file Excel
    try:
        workbook = openpyxl.load_workbook(EXCEL_FILE_NAME)
        sheet = workbook.active
    except FileNotFoundError:
        print(f"LỖI: Không tìm thấy file '{EXCEL_FILE_NAME}'.")
        return
    except Exception as e:
        print(f"LỖI: Không thể mở file Excel. {e}")
        return

    # Lấy chỉ số cột (ví dụ 'C' -> 3)
    col_index = column_index_from_string(COLUMN_LETTER)

    # 3. Thu thập tất cả URL từ cột C
    urls_to_download = []
    for row in sheet.iter_rows(min_row=1, min_col=col_index, max_col=col_index):
        cell_value = row[0].value
        if cell_value and (str(cell_value).startswith('http://') or str(cell_value).startswith('https://')):
            urls_to_download.append(cell_value)

    if not urls_to_download:
        print(f"Không tìm thấy URL hợp lệ nào trong cột {COLUMN_LETTER}.")
        return

    print(f"Tìm thấy tổng cộng {len(urls_to_download)} URL. Bắt đầu tải...")

    # 4. Tải từng ảnh
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
    }

    for index, url in enumerate(urls_to_download):
        try:
            # Dùng stream=True để tải file lớn hiệu quả
            response = requests.get(url, headers=headers, stream=True, timeout=10)

            # Kiểm tra lỗi HTTP (ví dụ: 404 Not Found, 403 Forbidden)
            response.raise_for_status()

            # Lấy định dạng ảnh từ header
            content_type = response.headers.get('content-type', '').lower()
            extension = get_file_extension(content_type)

            # Tạo tên file (ví dụ: image_00001.jpg)
            # Dùng f-string với zero-padding (ví dụ: :05d = 5 chữ số, điền số 0)
            file_name = f"image_{index + 1:05d}{extension}"
            file_path = os.path.join(DOWNLOAD_FOLDER, file_name)

            # Ghi file ảnh
            with open(file_path, 'wb') as f:
                for chunk in response.iter_content(chunk_size=8192):
                    f.write(chunk)

            print(f"[THÀNH CÔNG] Đã tải: {url} -> {file_path}")

        except requests.exceptions.HTTPError as e:
            print(f"[LỖI HTTP] Không thể tải {url}. Lỗi: {e.response.status_code}")
        except requests.exceptions.RequestException as e:
            print(f"[LỖI KẾT NỐI] Không thể tải {url}. Lỗi: {e}")
        except Exception as e:
            print(f"[LỖI KHÁC] Xảy ra lỗi với {url}. Lỗi: {e}")

    print("\nHoàn tất tải xuống!")

# Chạy hàm chính
if __name__ == "__main__":
    download_images_from_excel()
```

B2: Tạo folder images để chứa hình hảnh sau khi tải

B3: Thêm file data (lưu ý phải cùng cấp với main.py và folder images

#### Cài đặt môi trường và chạy dự án

**Cài đặt môi trường ảo**

`python3 -m venv venv`

**Kích hoạt môi trường ảo**

`source venv\bin\activate`

**Chạy Script**

`python3 main.py`
