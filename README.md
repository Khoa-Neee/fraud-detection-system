# Swinburne Hackathon: Advanced Fraud Detection System

## Tổng quan dự án (Project Overview)
Hệ thống phát hiện gian lận giao dịch ngân hàng theo thời gian thực (Real-time Fraud Detection), ứng dụng kiến trúc đa cơ sở dữ liệu (Multi-Database Architecture) và AI Agents để quy trình phân tích rủi ro đa chiều đạt độ chính xác cao nhất mà không hi sinh trải nghiệm người dùng hợp lệ.

Dự án bao gồm 2 phần chính:
- **Backend:** Ứng dụng mô hình AI Agents (Planner, Vision, Detective, Report) kết hợp với 4 cơ sở dữ liệu chuyên biệt (Redis, MongoDB, Neo4j, ChromaDB).
- **Frontend:** Ứng dụng di động mô phỏng Mobile Banking trực quan, xây dựng bằng React Native (Expo).

---

## 1. Cài đặt môi trường & Clone Code

```bash
# Clone repository
git clone <url-repo-cua-ban>
cd swinburne
```

---

## 2. Thiết lập Backend (4 Databases & API Keys)

### 2.1 Cài đặt thư viện Python
Mở terminal, di chuyển vào thư mục Backend và tạo môi trường ảo:
```bash
cd backend
python -m venv venv

# Kích hoạt venv (Windows):
venv\Scripts\activate
# Kích hoạt venv (Mac/Linux):
# source venv/bin/activate

pip install -r requirements.txt
```

### 2.2 Đăng ký API Keys & Databases
Bạn cần tạo tài khoản tự do (Free Tier) tại 4 nền tảng sau và lấy thông tin cấu hình:
1. **Redis Enterprise Cloud**: Lưu trữ dữ liệu screening cực nhanh cho Phase 1.
2. **Neo4j AuraDB**: Cơ sở dữ liệu đồ thị (Graph DB) để phân tích hành vi và luồng tiền ngầm (hiện ẩn danh).
3. **MongoDB Atlas**: Tương đương CSDL lõi của ngân hàng, lưu trữ hồ sơ người dùng (Profiles) và lịch sử giao dịch gốc.
4. **ChromaDB Cloud / Local**: Lưu trữ Knowledge Base lưu giữ các mẫu gian lận (Fraud Patterns).
5. **Google Gemini API**: Lấy API Key từ Google AI Studio để cấp luồng suy luận cho các AI Agents.

### 2.3 Cấu hình `.env`
Tạo file `.env` trong thư mục `backend` (có thể copy từ mẫu `.env.example`):
```bash
cp .env.example .env
```
Mở file `.env` và điền đầy đủ các thông tin Credentials / API Keys bạn vừa khởi tạo ở trên.

### 2.4 Sinh Dữ liệu và Đẩy (Push) lên Databases
Bước này giúp đổ các dữ liệu mô phỏng (Profiles, Transactions, Relationships, Rule) vào các DB để có dữ liệu thực nghiệm:
```bash
# Hệ thống sẽ đọc file dữ liệu mẫu và push tự động lên 4 cơ sở dữ liệu tương ứng
python setup_demo.py
```

---

## 3. Khởi chạy Ứng dụng

### 3.1 Chạy Backend Server
Giữ nguyên terminal đang ở thư mục `backend`, khởi chạy server FastAPI:
```bash
python main.py --serve
```

### 3.2 Cấu hình kết nối API cho Frontend
Chạy ứng dụng di động trên điện thoại thật yêu cầu bạn phải trỏ đúng địa chỉ IP LAN của máy tính đang chạy Backend:
1. Mở ứng dụng **Command Prompt (cmd)** trên Windows và gõ lệnh `ipconfig`.
2. Tìm dòng **IPv4 Address** của mạng Wi-Fi/LAN bạn đang kết nối (ví dụ: `192.168.1.10`).
3. Mở file mã nguồn `frontend/src/services/api.js`.
4. Sửa giá trị của biến `BACKEND_HOST` bằng dải IPv4 mà bạn vừa lấy được. *(Lưu ý: Không được để là `localhost` vì điện thoại của bạn sẽ không hiểu `localhost` là máy tính).*

### 3.3 Khởi chạy Frontend (Expo)
Mở một Terminal **mới**, di chuyển vào trực tiếp thư mục `frontend`:
```bash
cd frontend
npm install
npx expo start -c
```

### 3.4 Trải nghiệm trên Điện thoại qua Expo Go
- **Đối với iOS:** Truy cập **App Store** và kiếm ứng dụng tên **"Expo Go"**. Tải về, sau đó mở ứng dụng Camera gốc của iPhone quét mã QR ở trên Terminal của bước 3.2.
- **Đối với Android:** Tải **"Expo Go"** từ **Google Play**. Mở ứng dụng Expo Go lên và chọn tính năng quét mã QR, sau đó quét mã hiển thị trên Terminal.

*Lưu ý bắt buộc: Điện thoại và Máy tính (chạy backend & expo) phải được kết nối chung vào CÙNG MỘT MẠNG Wi-Fi/LAN.*

---

## 4. Hướng dẫn Test Kịch Bản Demo (Demo Scenarios)

Trên ứng dụng điện thoại, tiến hành **Đăng nhập (Sign In)** bằng định danh:
👉 `C1003668831` *(Huỳnh Vinh Hải - Khách hàng "thanh niên nghiêm túc", User uy tín có Low Risk).*

Nhấn vào **Chuyển tiền trong nước**. Sau đó, trải nghiệm kiểm tra sức mạnh hệ thống với 2 kịch bản chuyển khoản sau:

### Kịch bản 1: Giao dịch có chủ ý đáng ngờ (Khởi động AI chuyên sâu)
Gửi tiền đến một tài khoản làm cầu nối, thuộc mạng lưới ngầm (từ dấu hiệu bạn cung cấp qua ảnh chụp giấy tờ), nhưng trên hệ thống vẫn đang được giả vờ là "chưa bị lộ trong Blacklist".
- **Tới (Receiver / To Account ID):** `C1102413633` *(Lê Hải Vinh - Nút thắt mạng lưới ngầm)*
- **Số tiền (Amount):** `5000` *(Con số cố ý áp sát nhưng nằm dưới mốc $10k để đánh lừa bộ báo cáo chống rửa tiền tiêu chuẩn)*
- **Nội dung (Description):** `Thanh toan don hang`

**Kết quả kỳ vọng:** Do né được chặn cứng Phase 1 nhờ uy tín 2 bên và số tiền lách luật, hệ thống bắt buộc kích hoạt Phase 2 gọi các AI Agent. Detective Agent sẽ huyênh động GraphDB và Vision nhận diện quan hệ ẩn để kết luận giao dịch này rủi ro cao và đưa ra giải trình chặt chẽ để **Chặn (Block)**.

### Kịch bản 2: Giao dịch sinh hoạt thông thường (Tốc độ cao)
Khách hàng tiếp tục thao tác chuyển một khoản tiền nhỏ, hợp lý đến một người dùng uy tín khác.
- **Tới (Receiver / To Account ID):** `C1004838919` *(Bùi Uyên An - Một khách hàng cũng có mức độ uy tín cao)*
- **Số tiền (Amount):** `10` *(Con số nhỏ, phù hợp chi tiêu mua sắm/ăn uống)*
- **Nội dung (Description):** `Tra tien ca phe`

**Kết quả kỳ vọng:** Giao dịch qua ngay bộ lọc Screening tốc độ siêu cao ở Phase 1 bằng Redis, cho ra điểm rủi ro cực thấp và **Duyệt ngay lập tức (Approve)**. Giao dịch diễn ra trơn tru mà không tốn chi phí và thời gian gọi bộ máy AI khổng lồ.
