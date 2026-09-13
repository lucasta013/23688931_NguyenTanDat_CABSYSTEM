# API Specification – Hệ thống CAB (23688931_NguyenTanDat_CABSYSTEM)

> Tài liệu tổng quan API, dùng cho BA/stakeholder tra cứu nhanh. Chi tiết kỹ thuật (schema, request/response mẫu) xem thêm `openapi.yaml`.

## 1. Quy ước chung

| Mục | Quy ước |
|---|---|
| Base URL | `/api/v1` |
| Định dạng dữ liệu | JSON |
| Xác thực | Bearer Token (JWT) trong header `Authorization: Bearer <token>` — áp dụng RN01/FR41 |
| Phân quyền | Theo vai trò `customer` / `driver` / `staff` (`staff` chia `staff_basic` và `staff_senior` theo RN24, RN25) — FR42 |
| Định dạng lỗi | `{ "error": { "code": "string", "message": "string" } }` |
| Phân trang (API danh sách) | Query `?page=&limit=`, trả kèm `meta.total`, `meta.page` |
| Cập nhật real-time | Trạng thái chuyến đi / vị trí tài xế đẩy qua kênh WebSocket riêng (`/ws/trips/{tripId}`), nằm ngoài phạm vi REST liệt kê dưới đây |
| Audit log | Mọi thao tác nhạy cảm (staff_senior) được ghi log tự động ở tầng hệ thống theo FR43/RN26, không cần client gọi riêng |

> **Lưu ý mở:** RN20 (giới hạn đặt xe khi còn giao dịch chưa hoàn tất) và RN28 (tần suất cập nhật GPS) chưa chốt — các endpoint liên quan (`POST /trips`, `POST /drivers/{id}/location`) có thể cần điều chỉnh rule sau khi BA xác nhận với stakeholder.

## 2. Module: Xác thực & Tài khoản

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| POST | `/auth/register` | Đăng ký tài khoản bằng SĐT/email | FR01 | Public |
| POST | `/auth/login` | Đăng nhập | FR02, UC01 | Public |
| GET | `/users/me` | Xem thông tin cá nhân | FR03 | customer, driver |
| PUT | `/users/me` | Cập nhật thông tin cá nhân | FR03, UC02 | customer, driver |

## 3. Module: Đặt chuyến đi

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/trips/estimate` | Xem giá ước tính theo điểm đón/đến/loại xe | FR05 | customer |
| POST | `/trips` | Gửi yêu cầu đặt xe (điểm đón, điểm đến, loại xe) | FR04, FR05, FR06, UC03 | customer |

## 4. Module: Theo dõi chuyến đi

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/trips/{tripId}` | Xem trạng thái hiện tại, thông tin tài xế, ETA | FR07, FR08, FR09, UC04 | customer |
| GET | `/trips/{tripId}/status-history` | Xem lịch sử thay đổi trạng thái | FR10 | customer, staff |

## 5. Module: Lịch sử & Đánh giá

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/customers/{id}/trips` | Xem lịch sử chuyến đi | FR11, UC05 | customer |
| GET | `/trips/{tripId}` | Xem chi tiết một chuyến đi | FR12 | customer |
| POST | `/trips/{tripId}/rating` | Đánh giá & nhận xét tài xế (chỉ 1 lần – AC12) | FR13, UC06 | customer |

## 6. Module: Hồ sơ & trạng thái tài xế

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| POST | `/drivers/register` | Đăng ký/khởi tạo tài khoản tài xế | FR14, UC08 | Public (driver) |
| PUT | `/drivers/{id}/profile` | Cập nhật hồ sơ & thông tin phương tiện | FR15 | driver |
| PATCH | `/drivers/{id}/availability` | Chuyển trạng thái sẵn sàng / không sẵn sàng | FR16, UC09 | driver |

## 7. Module: Lời mời chuyến & tiến trình (Tài xế)

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/drivers/{id}/trip-invitations` | Xem lời mời chuyến đang chờ phản hồi | FR17 | driver |
| POST | `/trips/{tripId}/invitations/accept` | Chấp nhận lời mời chuyến | FR18, UC10 | driver |
| POST | `/trips/{tripId}/invitations/reject` | Từ chối lời mời chuyến | FR18 | driver |
| PATCH | `/trips/{tripId}/status` | Cập nhật tiến trình chuyến (đến điểm đón → đón khách → di chuyển → hoàn thành), không cho bỏ bước (RN13) | FR19, UC11 | driver |

## 8. Module: Vị trí tài xế (GPS)

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| POST | `/drivers/{id}/location` | Ghi nhận vị trí tài xế real-time | FR20 | driver |

> FR21–FR23 (tìm & phân công tài xế, tự động chuyển tài xế khi bị từ chối, thông báo hết tài xế) là logic **nội bộ hệ thống**, không expose thành API công khai — được kích hoạt tự động sau `POST /trips` và sau khi nhận `reject`/timeout.

## 9. Module: Cước & Thanh toán

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/trips/{tripId}/fare` | Xem chi tiết cước phí sau chuyến | FR24, FR25 | customer |
| POST | `/trips/{tripId}/payments` | Thực hiện thanh toán (chọn tiền mặt/điện tử) | FR26, FR27, FR29, UC07 | customer |
| GET | `/payments/{paymentId}` | Xem trạng thái giao dịch thanh toán | FR29 | customer, staff |

> FR28 (không lưu thông tin thẻ/tài khoản thanh toán – RN18): schema `Payment` không có field số thẻ; giao dịch điện tử xử lý qua cổng thanh toán bên thứ 3, hệ thống chỉ lưu `payment_id`, `method`, `status`.

## 10. Module: Thông báo

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/users/me/notifications` | Xem danh sách thông báo đã nhận | FR30, FR31, UC19 | customer, driver |

> Việc gửi thông báo (5 mốc sự kiện – RN21) là tác vụ hệ thống chạy nền (module độc lập theo RN23/NFR08), không có endpoint client gọi để "tạo" thông báo.

## 11. Module: Quản trị vận hành

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/admin/customers` | Danh sách & quản lý khách hàng | FR33, UC14 | staff |
| GET | `/admin/drivers` | Danh sách & quản lý tài xế | FR33, UC14 | staff |
| GET | `/admin/vehicles` | Danh sách & quản lý phương tiện | FR33, UC14 | staff |
| GET | `/admin/trips/live` | Giám sát chuyến đi đang diễn ra real-time | FR34, UC15 | staff |
| POST | `/admin/trips/{tripId}/incidents` | Ghi nhận & xử lý sự cố chuyến đi (thao tác nhạy cảm – RN25) | FR35, UC16 | staff_senior |
| GET | `/admin/transactions` | Tra cứu lịch sử giao dịch | FR36, UC17 | staff |
| GET | `/admin/audit-logs` | Xem log thao tác nhạy cảm | FR43, RN26 | staff_senior |

## 12. Module: Báo cáo

| Method | Endpoint | Mô tả | FR/UC | Vai trò |
|---|---|---|---|---|
| GET | `/reports/trips?from=&to=` | Báo cáo số lượng chuyến đi | FR37, UC18 | staff |
| GET | `/reports/revenue?from=&to=` | Báo cáo doanh thu | FR38, UC18 | staff |
| GET | `/reports/completion-rate?from=&to=` | Báo cáo tỷ lệ hoàn thành/hủy chuyến | FR39, UC18 | staff |
| GET | `/reports/driver-performance?from=&to=` | Báo cáo hiệu quả hoạt động tài xế | FR40, UC18 | staff |

---

**Bảng quy đổi vai trò:**
- `customer` – khách hàng đã đăng nhập
- `driver` – tài xế đã đăng nhập, đã xác minh hồ sơ (RN03)
- `staff` – nhân viên vận hành (mặc định chỉ xem – RN24)
- `staff_senior` – quản lý cấp cao, được thao tác nhạy cảm (RN25)
