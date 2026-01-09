---
title : "Worklog tuần 9"
date :  "`r Sys.Date()`" 
weight : 9
pre: <b> 1.9 </b>
chapter : false
---

## 📍 Mục tiêu tuần 9
- Thiết kế schema Notification service theo nghiệp vụ list read read-all stream.
- Thiết kế index theo truy vấn chính theo user và thời gian.
- Chọn cơ chế read marker và read receipt để giảm write.
- Khởi tạo notification-service và chạy migration.
- Hoàn thành API core list stream read read-all.
- Tích hợp Kafka giữa event-service và notification-service.
- Chuyển email sang notification-service và thiết kế chống gửi trùng.
- Tích hợp FE với list read read-all.
- Tích hợp FE với SSE stream và xử lý dedup.
- Tạo backlog bug và fix nhóm blocker đầu tiên.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Thiết kế schema Notification service gồm notification notification_read_marker notification_read_receipt<br>- Thiết kế soft delete bằng deleted_at và hết hạn bằng expires_at<br>- Thiết kế index theo (user_uuid created_at desc uuid desc) và expires_at | 01/12/2025 | 01/12/2025 |  |
| 3 | - Tạo project notification-service và chạy migration<br>- Tạo layer code controller service repository dto config<br>- Triển khai API core list stream read read-all<br>- Áp dụng sort tie breaker theo (created_at desc uuid desc) | 02/12/2025 | 02/12/2025 |  |
| 4 | - Thiết lập Kafka producer ở event-service khi tạo event và khi đăng ký event<br>- Thiết lập Kafka consumer ở notification-service và lưu notification vào DB<br>- Test end to end bằng Postman qua list API<br>- Thiết kế notification_key để chống tạo trùng khi retry | 03/12/2025 | 03/12/2025 |  |
| 5 | - Loại bỏ gửi email đồng bộ trong event-service<br>- Publish message qua Kafka và gửi email trong notification-service theo message type<br>- Thiết kế idempotency key và quy tắc commit offset sau khi persist và gửi mail<br>- Phân loại lỗi mail tạm thời và lỗi vĩnh viễn | 04/12/2025 | 04/12/2025 |  |
| 6 | - Tích hợp FE với GET /api/v1/notifications có phân trang<br>- Tích hợp FE với POST read và POST read-all<br>- Sửa mismatch field name và timestamp format giữa FE và BE<br>- Chuẩn hóa DTO và types | 05/12/2025 | 05/12/2025 |  |
| 7 | - Tích hợp SSE GET /api/v1/notifications/stream vào FE<br>- Xử lý reconnect cơ bản và đóng kết nối khi logout<br>- Dedup theo notification uuid khi merge list và stream<br>- Đồng bộ read state khi có notification mới | 06/12/2025 | 06/12/2025 |  |
| CN | - Tạo backlog bug theo trang và mức độ gồm auth discover event detail create event registration notification settings<br>- Ghi bước tái hiện và expected result<br>- Fix nhóm bug blocker gồm null image deep_link sai read-all không update spacing lệch<br>- Chuẩn hóa mapping object giữa FE và BE | 07/12/2025 | 07/12/2025 |  |

---

## ✅ Kết quả đạt được tuần 9
- Hoàn thành schema Notification service.
  - Có 3 bảng notification notification_read_marker notification_read_receipt.
  - Read-all dùng read marker theo thời gian.
  - Read single dùng read receipt theo từng notification.
  - Có deleted_at expires_at deep_link priority.

- Hoàn thành notification-service và API core.
  - Migration chạy thành công.
  - GET /api/v1/notifications trả list theo user và phân trang.
  - GET /api/v1/notifications/stream trả SSE.
  - POST /api/v1/notification/read tạo read receipt.
  - POST /api/v1/notification/read-all cập nhật read marker.
  - Sort ổn định theo created_at desc uuid desc.

- Hoàn thành tích hợp Kafka.
  - Event-service publish message theo sự kiện tạo event và đăng ký event.
  - Notification-service consume và tạo record notification.
  - Có thiết kế notification_key để giảm rủi ro tạo trùng.

- Chuyển email sang notification-service.
  - Event-service chỉ xử lý nghiệp vụ và publish message.
  - Notification-service gửi email theo message type.
  - Có kế hoạch idempotency và tránh double send khi scale consumer.

- Tích hợp FE với Notification service.
  - FE hiển thị list có phân trang.
  - FE hỗ trợ read và read-all.
  - FE nhận notification mới qua SSE và merge vào list.
  - FE dedup theo uuid và đồng bộ read state.

- Bắt đầu giai đoạn ổn định chất lượng FE.
  - Có backlog bug theo trang và mức độ.
  - Fix nhóm blocker đầu tiên và chuẩn hóa mapping field FE BE.