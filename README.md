# K26CNTT2_IT108_Session16_Ex02
Bài tập số 2
# 🛒 Bài 2: Xử lý Sự cố Cache và Phiên Đăng nhập trong Doanh Nghiệp

> **Môn học:** Nhập Môn Công Nghệ Thông Tin (IT108)  
> **Sinh viên thực hiện:** Lê Hồ Thanh Thảo  
> **Mã sinh viên:** N26DTCN030  
> **Lớp:** D26TXCN02-N (K26CNTT2)  

---

## 📌 PHẦN 1: PHÂN TÍCH NGUYÊN NHÂN SỰ CỐ

### 1. Tại sao file CSS mới không cập nhật khi ấn F5?
* **Cơ chế lưu trữ:** Trình duyệt mặc định sử dụng bộ nhớ đệm **Browser Cache** để lưu các tài nguyên tĩnh như CSS, JS, hình ảnh nhằm tối ưu tốc độ tải trang cho các lần truy cập sau.
* **Nguyên nhân dính giao diện cũ:** Khi bạn An bấm `F5` (hoặc `Ctrl + R`), trình duyệt vẫn thực hiện **Normal Reload** (tải lại thông thường). Do máy chủ chưa được cấu hình cơ chế hủy Cache (như `Cache-Control: no-cache` hay `Max-Age=0`), trình duyệt tự động lấy tệp CSS từ bộ nhớ đệm cục bộ (Disk Cache / Memory Cache) thay vì tải tệp CSS mới nhất từ Server về.

---

### 2. Tại sao phiên đăng nhập bị mất mỗi khi đóng trình duyệt?
* **Phân loại Cookies:** 
  * **Session Cookies (Cookie phiên):** Không chứa thuộc tính thời hạn (`Max-Age` hoặc `Expires`). Loại Cookie này chỉ tồn tại tạm thời trong bộ nhớ RAM và **tự động bị xóa sạch** ngay khi người dùng đóng toàn bộ trình duyệt.
  * **Persistent Cookies (Cookie bền vững):** Được cấu hình thuộc tính `Max-Age` (thời gian sống tính bằng giây) hoặc `Expires` (ngày giờ hết hạn cụ thể). Loại Cookie này được ghi cứng vào ổ đĩa và duy trì trạng thái đăng nhập ngay cả khi đóng trình duyệt hay khởi động lại máy.
* **Nguyên nhân văng tài khoản:** Mã nguồn của An khởi tạo mẩu tin Cookie định danh (Session ID / Access Token) nhưng **thiếu hoàn toàn thuộc tính `Max-Age` hoặc `Expires`**, khiến trình duyệt coi đây là **Session Cookie**. Do đó, mỗi khi An đóng trình duyệt, Cookie bị xóa dẫn tới mất phiên đăng nhập.

---

### 3. Bảng so sánh bản chất bộ nhớ đệm và cơ chế lưu trữ

| Cấu phần | Browser Cache (CSS/JS) | Session Cookies | Persistent Cookies |
| :--- | :--- | :--- | :--- |
| **Mục đích** | Lưu tài nguyên tĩnh để tải trang nhanh hơn | Lưu phiên làm việc tạm thời | Duy trì đăng nhập lâu dài |
| **Vị trí lưu trữ** | Disk Cache / RAM Cache | Bộ nhớ RAM (Tạm thời) | Ổ đĩa cục bộ (Disk Storage) |
| **Vòng đời (Lifetime)** | Phụ thuộc cấu hình `Cache-Control` | Tự xóa khi đóng trình duyệt | Tồn tại tới khi hết `Max-Age`/`Expires` |

---

## 🔄 PHẦN 2: SƠ ĐỒ VÒNG ĐỜI VÀ NGUYÊN LÝ HOẠT ĐỘNG

```mermaid
graph TD
    A[Gửi Yêu Cầu / Đóng Mở Web] --> B{Kiểm Tra Lưu Trữ}
    
    %% Nhánh Cache
    B -->|Tải File CSS| C{F5 Normal Reload?}
    C -->|Có| D[Dùng CSS Cũ Từ Browser Cache ❌]
    C -->|Hard Reload Ctrl+Shift+R| E[Tải CSS Mới Từ Server ✅]
    
    %% Nhánh Cookie
    B -->|Đóng Trình Duyệt| F{Cấu Hình Cookie?}
    F -->|Thiếu Max-Age / Expires| G[Session Cookie Tự Xóa -> Đăng Xuất ❌]
    F -->|Có Max-Age / Expires| H[Persistent Cookie Được Giữ -> Đăng Nhập ✅]
