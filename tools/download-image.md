```python
import os
import requests
import openpyxl
from openpyxl.utils import column_index_from_string
import concurrent.futures
import time
import threading

# --- Cấu hình ---
EXCEL_FILE_NAME = 'YOUR_EXCEL_FILE_HERE'
COLUMN_LETTER = 'C'        # <-- Cột chứa URL
DOWNLOAD_FOLDER = 'data/images' # <-- Thư mục gốc
MAX_WORKERS = 20           # <-- SỐ LUỒNG (THREADS) CHẠY CÙNG LÚC
IMAGES_PER_FOLDER = 10000  # <-- MỚI: Tự động đổi thư mục sau từng này ảnh
# ------------------

class DownloadManager:
    """
    Quản lý thư mục tải về một cách an toàn trong môi trường đa luồng.
    Nó sẽ tự động xoay vòng thư mục (ví dụ: images -> images1 -> images2)
    dựa trên SỐ LƯỢNG ảnh.
    """
    def __init__(self, base_folder_path):
        self.base_name = base_folder_path
        self.current_folder_index = 0 # 0 = images, 1 = images1, ...
        self.current_path = base_folder_path
        self.lock = threading.Lock()
        print(f"Thư mục tải về ban đầu: {self.current_path}")

    def get_path_for_index(self, image_index):
        """
        Tính toán và trả về đường dẫn thư mục chính xác cho một index ảnh cụ thể.
        Tự động tạo thư mục mới nếu cần.
        """
        
        # Tính toán xem index này nên ở thư mục số mấy
        # 0-19999 -> 0
        # 20000-39999 -> 1
        # 40000-59999 -> 2
        target_folder_index = image_index // IMAGES_PER_FOLDER

        # Nếu vẫn là thư mục hiện tại, chỉ cần trả về đường dẫn (nhanh)
        if target_folder_index == self.current_folder_index:
            return self.current_path

        # Nếu là thư mục mới, chúng ta cần 'lock' để tạo
        with self.lock:
            # Kiểm tra lại một lần nữa sau khi có lock
            # (Phòng trường hợp một luồng khác đã tạo thư mục trong lúc mình chờ)
            if target_folder_index == self.current_folder_index:
                return self.current_path

            # Đây là luồng đầu tiên chạm mốc mới, tiến hành tạo thư mục
            print(f"\n[INFO] Đã đạt mốc {IMAGES_PER_FOLDER} ảnh. Đang đổi sang thư mục mới...\n")
            
            self.current_folder_index = target_folder_index
            self.current_path = f"{self.base_name}{self.current_folder_index}"
            
            print(f"[INFO] Thư mục mới là: {self.current_path}")
            
            try:
                os.makedirs(self.current_path, exist_ok=True)
            except Exception as e:
                print(f"[LỖI NGHIÊM TRỌNG] Không thể tạo thư mục mới {self.current_path}. Lỗi: {e}")
            
            return self.current_path

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

def download_image(url, index, headers, manager):
    """
    Hàm này thực hiện việc tải MỘT ảnh.
    Nó sẽ hỏi manager xem nên lưu file vào đâu.
    """
    try:
        # 1. Tải ảnh vào bộ nhớ RAM trước
        response = requests.get(url, headers=headers, timeout=10)
        response.raise_for_status()
        
        # Giữ dữ liệu ảnh trong RAM
        image_data = response.content
        
        # Lấy định dạng ảnh từ header
        content_type = response.headers.get('content-type', '').lower()
        extension = get_file_extension(content_type)
        file_name = f"image_{index + 1:05d}{extension}"

    except requests.exceptions.HTTPError as e:
        print(f"[LỖI HTTP] Không thể tải {url}. Lỗi: {e.response.status_code}")
        return
    except requests.exceptions.RequestException as e:
        print(f"[LỖI KẾT NỐI] Không thể tải {url}. Lỗi: {e}")
        return
    except Exception as e:
        print(f"[LỖI TẢI] Xảy ra lỗi với {url}. Lỗi: {e}")
        return

    # 2. GHI file ra đĩa
    try:
        # Hỏi manager để lấy đường dẫn chính xác cho index này
        current_folder = manager.get_path_for_index(index)
        file_path = os.path.join(current_folder, file_name)
        
        # Ghi dữ liệu từ RAM ra đĩa
        with open(file_path, 'wb') as f:
            f.write(image_data)
        
        print(f"[THÀNH CÔNG] Đã tải: {url} -> {file_path}")

    except Exception as e:
        # Lỗi OS (ví dụ: hết dung lượng, không có quyền ghi)
        print(f"[LỖI GHI FILE] Không thể ghi file {file_path}. Lỗi: {e}")


def download_images_from_excel():
    # 1. Khởi tạo Manager
    # Thư mục gốc sẽ là 'data/images' (khi index=0)
    manager = DownloadManager(DOWNLOAD_FOLDER) 
    
    # Tạo thư mục ban đầu (ví dụ: 'data/images')
    os.makedirs(manager.current_path, exist_ok=True)
    
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

    # Lấy chỉ số cột
    col_index = column_index_from_string(COLUMN_LETTER)

    # 3. Thu thập tất cả URL từ cột C
    urls_to_download = []
    for row in sheet.iter_rows(min_row=1, min_col=col_index, max_col=col_index):
        cell_value = row[0].value
        if cell_value and (str(cell_value).startswith('http://') or str(cell_value).startswith('https://')):
            urls_to_download.append(str(cell_value))

    if not urls_to_download:
        print(f"Không tìm thấy URL hợp lệ nào trong cột {COLUMN_LETTER}.")
        return

    print(f"Tìm thấy tổng cộng {len(urls_to_download)} URL. Bắt đầu tải với {MAX_WORKERS} luồng...")
    print(f"Sẽ tự động đổi thư mục sau mỗi {IMAGES_PER_FOLDER} ảnh.")

    # 4. Headers chung cho tất cả request
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
    }

    # 5. Đa luồng
    start_time = time.time()
    
    with concurrent.futures.ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
        futures = []
        # Dùng enumerate để lấy 'index' (bắt đầu từ 0)
        for index, url in enumerate(urls_to_download):
            futures.append(
                # Truyền 'manager' và 'index' vào mỗi luồng
                executor.submit(download_image, url, index, headers, manager)
            )
        
        for future in concurrent.futures.as_completed(futures):
            pass 

    end_time = time.time()
    print(f"\nHoàn tất tải xuống! Tổng thời gian: {end_time - start_time:.2f} giây.")

# Chạy hàm chính
if __name__ == "__main__":
    download_images_from_excel()
```
