# 🚖 Tài liệu Kịch bản Kiểm thử (Test Cases) - CAB System

Tài liệu này bao gồm các kịch bản kiểm thử cho dự án hệ thống đặt xe CAB, dựa trên tài liệu Đặc tả Yêu cầu (SRS).

---

## 1. Login & Authentication (Đăng nhập & Xác thực)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-LOG-001 | Positive | Đăng nhập thành công với tài khoản hợp lệ | Tài khoản đã được đăng ký và kích hoạt. | 1. Mở app.<br>2. Nhập SĐT và mật khẩu.<br>3. Bấm Đăng nhập. | SĐT: `0901234567`<br>Pass: `ValidPass123` | Đăng nhập thành công, chuyển hướng đến màn hình chính (Trang chủ). (AC02) | High |
| TC-LOG-002 | Negative | Đăng nhập sai mật khẩu | Tài khoản đã tồn tại. | 1. Mở app.<br>2. Nhập SĐT đúng, mật khẩu sai.<br>3. Bấm Đăng nhập. | SĐT: `0901234567`<br>Pass: `WrongPass` | Hệ thống báo lỗi "Sai số điện thoại hoặc mật khẩu". Không cho phép đăng nhập. | High |
| TC-LOG-003 | Empty | Bỏ trống các trường bắt buộc | Đang ở màn hình Đăng nhập. | 1. Để trống SĐT hoặc mật khẩu.<br>2. Bấm Đăng nhập. | SĐT: `(trống)`<br>Pass: `(trống)` | Nút Đăng nhập bị vô hiệu hóa hoặc hiển thị lỗi "Vui lòng nhập đầy đủ thông tin". | Medium |

## 2. Register (Đăng ký)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-REG-001 | Positive | Đăng ký tài khoản khách hàng mới | SĐT chưa từng đăng ký. | 1. Chọn Đăng ký.<br>2. Nhập thông tin.<br>3. Xác thực OTP. | SĐT: `0987654321`<br>Tên: `Nguyen Van A` | Tạo tài khoản thành công, lưu vào DB. (AC01) | High |
| TC-REG-002 | Negative | Đăng ký với SĐT đã tồn tại | SĐT `0901234567` đã đăng ký. | 1. Chọn Đăng ký.<br>2. Nhập SĐT đã tồn tại.<br>3. Bấm Tiếp tục. | SĐT: `0901234567` | Hệ thống từ chối và báo lỗi "Số điện thoại đã được sử dụng". (AC02) | High |
| TC-REG-003 | Boundary | Mật khẩu quá ngắn | Màn hình nhập mật khẩu đăng ký. | 1. Nhập mật khẩu < 5 ký tự. | Pass: `1234` | Hệ thống báo lỗi "Mật khẩu phải từ 5 ký tự trở lên". | Medium |
| TC-REG-004 | Format | Sai định dạng số điện thoại | Màn hình đăng ký. | 1. Nhập SĐT sai định dạng chữ. | SĐT: `090abcd123` | Báo lỗi "Định dạng số điện thoại không hợp lệ". | Low |

## 3. Book Trip (Đặt chuyến đi)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-TRIP-001 | Positive | Đặt chuyến đi hợp lệ | Đã đăng nhập tài khoản Khách hàng. | 1. Chọn điểm đón, điểm đến.<br>2. Chọn loại xe (Standard).<br>3. Bấm Đặt xe. | Đón: `A`, Đến: `B`, Xe: `Standard` | Hệ thống chấp nhận yêu cầu, chuyển sang trạng thái "Đang tìm tài xế". (AC04) | High |
| TC-TRIP-002 | Empty | Thiếu thông tin điểm đến | Đã đăng nhập. | 1. Chọn điểm đón.<br>2. Để trống điểm đến.<br>3. Bấm Đặt xe. | Đón: `A`, Đến: `(trống)` | Hệ thống chặn và hiển thị lỗi "Vui lòng chọn điểm đến". (AC05, RN05) | High |
| TC-TRIP-003 | Negative | Đặt xe khi đang có chuyến Active | Khách hàng đang trong 1 chuyến đi chưa hoàn thành. | 1. Mở app.<br>2. Thử tạo yêu cầu chuyến mới. | Chuyến hiện tại: `Đang diễn ra` | Hệ thống chặn, báo lỗi "Bạn đang có chuyến đi chưa hoàn thành". (AC06, RN04) | High |
| TC-TRIP-004 | Boundary | Khoảng cách di chuyển quá xa (ngoại tỉnh) | Đã đăng nhập. | 1. Chọn điểm đón HCM, điểm đến Hà Nội. | Đón: `HCM`, Đến: `HN` | Cảnh báo "Khoảng cách quá xa, không hỗ trợ dịch vụ". | Medium |

## 4. Driver Invitation (Điều phối & Nhận chuyến)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-INV-001 | Positive | Tài xế nhận chuyến thành công | Tài xế ở trạng thái "Sẵn sàng". Có yêu cầu đặt xe gần đó. | 1. Tài xế nhận thông báo mời chuyến.<br>2. Bấm Chấp nhận. | N/A | Chuyến được gán cho tài xế. Khách hàng nhận được thông báo + ETA. (AC16) | High |
| TC-INV-002 | Error | Tài xế không phản hồi (Timeout) | Tài xế nhận được lời mời. | 1. Để màn hình mời chuyến đếm ngược hết giờ (timeout). | Timeout: `30s` | Tự động chuyển chuyến cho tài xế phù hợp tiếp theo. Tài xế hiện tại mất lượt. (AC17, RN09) | High |
| TC-INV-003 | Error | Concurrency (Xung đột nhận chuyến) | Broadcast tới nhiều tài xế. | 2 tài xế cùng bấm Chấp nhận chính xác cùng 1 lúc. | N/A | Tài xế bấm trước nhận chuyến. Tài xế sau nhận thông báo "Chuyến đi đã được nhận bởi người khác". | High |

## 5. Trip Tracking & Execution (Theo dõi & Thực hiện chuyến)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-TRK-001 | Positive | Cập nhật trạng thái tuần tự | Chuyến đi đã được gán. | 1. TX bấm Đã đến điểm đón.<br>2. Đã đón khách.<br>3. Hoàn thành. | Trạng thái tuần tự | Trạng thái cập nhật real-time cho cả App KH và App TX. (AC09, RN13) | High |
| TC-TRK-002 | Error | Mất kết nối mạng khi đang thực hiện chuyến | Đang trong chuyến đi. | 1. Tắt 3G/Wifi của tài xế.<br>2. Di chuyển.<br>3. Bật lại mạng. | N/A | Hệ thống tự động đồng bộ lại vị trí và trạng thái chuyến đi (NFR04). | High |
| TC-TRK-003 | Negative | Bỏ qua bước trạng thái | Chuyến đi vừa được gán (Đang di chuyển đến điểm đón). | 1. Tài xế cố tình gọi API bấm "Hoàn thành chuyến" ngay lập tức. | API gọi bypass UI | Hệ thống từ chối cập nhật, trả về lỗi 400 "Trạng thái không hợp lệ". (AC18) | Medium |

## 6. Fare & Payment (Tính cước & Thanh toán)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-PAY-001 | Positive | Thanh toán tiền mặt thành công | Chuyến đi hoàn thành, chọn Tiền mặt. | 1. Tài xế xác nhận đã nhận tiền mặt.<br>2. Đóng chuyến. | PTTT: `Tiền mặt` | Trạng thái thanh toán đổi thành "Hoàn tất". (AC24) | High |
| TC-PAY-002 | Error | Thanh toán qua thẻ bị Timeout/Thất bại | Chuyến đi hoàn thành, thanh toán Thẻ. | 1. Gọi API thanh toán bên thứ 3 giả lập thất bại. | Cổng TT báo lỗi | Chuyến đi vẫn đóng (Hoàn thành), hiển thị thanh toán "Chưa hoàn tất" / Báo lỗi cho KH thử lại. (AC23, RN19) | High |

## 7. History & Rating (Lịch sử & Đánh giá)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-HIS-001 | Positive | Khách hàng đánh giá tài xế | Chuyến đi đã hoàn thành và thanh toán. | 1. Vào màn hình đánh giá.<br>2. Chọn 5 sao, nhập nhận xét.<br>3. Gửi. | Điểm: `5`, Comment: `Tốt` | Đánh giá được lưu thành công, hiển thị trong hồ sơ tài xế. (AC11) | Medium |
| TC-HIS-002 | Boundary | Điểm đánh giá ngoài khoảng cho phép | Giao diện đánh giá chặn qua UI, dùng Postman gọi API. | 1. Gửi request đánh giá với điểm số = 6 hoặc = 0. | Điểm: `6` hoặc `0` | Hệ thống API từ chối, trả về lỗi 400 "Điểm đánh giá phải từ 1 đến 5". | Low |
| TC-HIS-003 | Negative | Đánh giá trùng lặp | Chuyến đi đã được đánh giá. | 1. Khách hàng dùng API thử gửi lại đánh giá lần 2. | Cùng `trip_id` | Hệ thống từ chối, báo lỗi "Chuyến đi này đã được đánh giá". (AC12) | Medium |

## 8. Admin Operations & Reports (Vận hành & Báo cáo)

| Test Case ID | Test Scenario | Test Case | Preconditions | Test Steps | Test Data | Expected Result | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TC-ADM-001 | Positive | Xem danh sách chuyến đi real-time | Đăng nhập tài khoản Admin/Staff. | 1. Truy cập Dashboard.<br>2. Mở tab "Chuyến đi đang diễn ra". | N/A | Hiển thị đúng danh sách các chuyến đi đang hoạt động trên bản đồ/bảng. (AC27) | High |
| TC-ADM-002 | Negative | Nhân viên thường thao tác nhạy cảm | Đăng nhập tài khoản Staff (Không có quyền Quản lý). | 1. Cố gắng thay đổi trạng thái khóa tài khoản của 1 tài xế. | N/A | Hệ thống từ chối truy cập (Access Denied / 403 Forbidden). (AC28, RN24) | High |

---
*Ghi chú: Markdown này được tổng hợp từ file CAB_Test_Cases_Full.xlsx, tối ưu hóa để hiển thị trực tiếp trên kho lưu trữ mã nguồn.*