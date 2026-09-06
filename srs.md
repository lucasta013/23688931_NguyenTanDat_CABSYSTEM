## 📋 Tổng quan dự án

### Bối cảnh
Công ty ABC hiện cung cấp dịch vụ đặt xe qua tổng đài và ứng dụng đơn giản, nhưng còn hạn chế: phân công tài xế thủ công, khó theo dõi chuyến đi, thanh toán chưa tập trung, khó mở rộng hệ thống.

### Mục tiêu dự án
Xây dựng nền tảng CAB mới có khả năng:
- Phục vụ số lượng lớn khách hàng & tài xế
- Tự động hóa tìm và phân công tài xế
- Quản lý tập trung chuyến đi, thanh toán, người dùng
- Kiến trúc linh hoạt, dễ mở rộng dịch vụ/thanh toán/thông báo trong tương lai

### Phạm vi
Ba nhóm người dùng chính: **Khách hàng**, **Tài xế**, **Nhân viên vận hành**.
Luồng nghiệp vụ chính: đặt xe → tìm & phân công tài xế → thực hiện chuyến → tính cước & thanh toán → thông báo → đánh giá.

### Thời gian triển khai
**7 tuần** (từ phân tích yêu cầu đến triển khai sản phẩm).

### Vai trò Business Analyst
Làm rõ phạm vi, tác nhân, quy trình nghiệp vụ, yêu cầu chức năng/phi chức năng, quy tắc nghiệp vụ, ngoại lệ và các vấn đề chưa chốt với các bên liên quan trước khi Dev triển khai.
## 📌 Phân tích Stakeholder

### Stakeholder chính (Primary)

| Stakeholder | Vai trò | Ảnh hưởng | Quan tâm | Kỳ vọng chính |
|---|---|---|---|---|
| Khách hàng | Đặt xe, theo dõi chuyến, thanh toán, đánh giá | Trung bình | Cao | Trải nghiệm đặt xe nhanh, minh bạch |
| Tài xế | Nhận/thực hiện chuyến, cập nhật trạng thái | Trung bình | Cao | Nhận chuyến kịp thời, phân chuyến công bằng |
| Nhân viên vận hành | Quản trị khách hàng/tài xế/phương tiện, xử lý sự cố | Cao | Cao | Giao diện quản trị, báo cáo, phân quyền |

### Stakeholder gián tiếp (Secondary)

| Stakeholder | Vai trò | Ảnh hưởng | Quan tâm | Kỳ vọng chính |
|---|---|---|---|---|
| Ban giám đốc / Ban lãnh đạo | Sponsor, ra quyết định chiến lược | Rất cao | Cao | Hệ thống mở rộng, báo cáo doanh thu/hiệu suất |
| Business Analyst | Làm rõ yêu cầu, phạm vi, quy tắc nghiệp vụ | Cao | Cao | Thông tin đầy đủ để đặc tả chính xác |
| Nhóm phát triển | Xây dựng giải pháp kỹ thuật | Trung bình | Trung bình | Yêu cầu rõ ràng, kiến trúc modular |
| Nhà cung cấp thanh toán bên ngoài | Đối tác xử lý giao dịch điện tử | Trung bình | Thấp | Tích hợp API, không lưu dữ liệu nhạy cảm |
| Nhà cung cấp thông báo (tương lai) | Mở rộng kênh thông báo | Thấp | Thấp | Kiến trúc dễ mở rộng |

> **Ghi chú:** Ban lãnh đạo là stakeholder có ảnh hưởng cao nhất do quyết định phạm vi & ngân sách. Khách hàng và Tài xế là hai tác nhân vận hành lõi của hệ thống.


## 🎯 Business Context – Bối cảnh & Mục tiêu kinh doanh

### Mục đích kinh doanh
Công ty ABC muốn chuyển đổi từ mô hình đặt xe thủ công (tổng đài + app đơn giản) sang nền tảng công nghệ tự động hóa toàn diện, giải quyết các điểm nghẽn: phân công tài xế thủ công, thiếu minh bạch thông tin chuyến đi, dữ liệu thanh toán phân mảnh, khó mở rộng hệ thống.

### Mục tiêu kinh doanh

| Mục tiêu | Mô tả |
|---|---|
| Tăng năng lực phục vụ | Phục vụ lượng lớn khách hàng & tài xế, đáp ứng cao điểm |
| Tự động hóa vận hành | Loại bỏ phân công thủ công, giảm phụ thuộc tổng đài |
| Minh bạch trải nghiệm khách hàng | Theo dõi real-time toàn bộ vòng đời chuyến đi |
| Tập trung hóa dữ liệu | Thống nhất dữ liệu khách hàng/tài xế/chuyến đi/thanh toán phục vụ báo cáo |
| Sẵn sàng mở rộng lâu dài | Kiến trúc linh hoạt, dễ bổ sung dịch vụ/thanh toán/thông báo mới |

### Giá trị kinh doanh kỳ vọng
- Rút ngắn thời gian ghép chuyến, giảm tỷ lệ khách hàng rời bỏ
- Tăng giữ chân khách hàng & tài xế nhờ trải nghiệm ổn định
- Cung cấp dữ liệu hỗ trợ ra quyết định cho ban lãnh đạo
- Giảm chi phí vận hành nhờ tự động hóa

## 📝 Business Requirements (BR)

| Mã | Business Requirement |
|---|---|
| BR01 | Quản lý tài khoản khách hàng (đăng ký, đăng nhập, cập nhật thông tin) |
| BR02 | Đặt chuyến đi (điểm đón/đến, loại xe, gửi yêu cầu) |
| BR03 | Theo dõi trạng thái chuyến đi theo thời gian thực |
| BR04 | Xem lịch sử chuyến đi và đánh giá tài xế |
| BR05 | Quản lý tài khoản & hồ sơ tài xế (thông tin, phương tiện, trạng thái) |
| BR06 | Tài xế nhận/từ chối chuyến và cập nhật tiến trình chuyến đi |
| BR07 | Tìm và phân công tài xế tự động theo vị trí & trạng thái |
| BR08 | Tính cước chuyến đi tự động |
| BR09 | Thanh toán (tiền mặt & điện tử qua bên thứ 3) |
| BR10 | Thông báo cho khách hàng & tài xế theo các mốc sự kiện |
| BR11 | Quản trị vận hành: khách hàng, tài xế, phương tiện, chuyến đi |
| BR12 | Báo cáo kinh doanh & vận hành |
| BR13 | Bảo mật & phân quyền truy cập hệ thống |

## 🔄 Mô hình hóa quy trình nghiệp vụ (Business Process)

```mermaid
flowchart TD
A[Khách hàng đăng nhập tạo yêu cầu đặt xe] --> B[Hệ thống tìm tài xế phù hợp]
B --> C[Gửi thông báo mời chuyến đến tài xế]
C --> D{Tài xế phản hồi?}
D -- Từ chối hoặc không phản hồi --> B
D -- Không còn tài xế phù hợp --> E[Thông báo khách hàng không tìm được tài xế]
D -- Chấp nhận --> F[Thông báo khách hàng tài xế đã nhận chuyến]
F --> G[Tài xế di chuyển đến điểm đón]
G --> H[Cập nhật đã đến điểm đón]
H --> I[Cập nhật đã đón khách]
I --> J[Cập nhật đang di chuyển]
J --> K[Cập nhật hoàn thành chuyến]
K --> L[Hệ thống tính cước chuyến đi]
L --> M[Khách hàng thanh toán]
M --> N{Thanh toán thành công?}
N -- Thất bại --> O[Thông báo lỗi và xử lý lại theo chính sách]
O --> M
N -- Thành công --> P[Thông báo kết quả thanh toán]
P --> Q[Khách hàng đánh giá tài xế]
Q --> R[Lưu lịch sử chuyến đi]
S[Nhân viên vận hành giám sát và xử lý sự cố] -.-> G
S -.-> K
```
