---
title : "Worklog tuần 8"
date :  "`r Sys.Date()`" 
weight : 8
pre: <b> 1.8 </b>
chapter : false
---

## 📍 Mục tiêu tuần 8
- Generate thêm UI từ template và bắt đầu port sang NextJS.
- Hoàn tất port các trang chính sang NextJS App Router.
- Ổn định UI theo token OpenSea và gửi repo cho team review.
- Thống nhất cấu trúc package cho Event service.
- Thiết kế API spec thô cho Event service theo nhóm endpoint.
- Phát triển list API và xử lý lỗi Lazy loading bằng DTO.
- Phát triển event detail API theo versioning current_version_uuid.
- Mở rộng list API với query params cho filter.
- Phát triển interest và registration endpoints.
- Gửi email xác nhận khi đăng ký event.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Generate thêm UI bằng AI Studio theo template<br>- Gom component trùng và chuẩn hóa naming cơ bản<br>- Khởi tạo NextJS và setup Tailwind<br>- Port layout khung gồm header sidebar routing<br>- Tạo trang Discover placeholder để test | 24/11/2025 | 24/11/2025 |  |
| 3 | - Port component chính sang NextJS gồm header sidebar modal tabs<br>- Port các trang Discover Interest Activity theo App Router<br>- Tách layout.tsx theo nhóm trang<br>- Fix lỗi routing link và alias import<br>- Dọn cấu trúc folder app và components | 25/11/2025 | 25/11/2025 |  |
| 4 | - Rà layout theo token OpenSea và fix spacing grid modal behavior<br>- Dọn code không dùng và viết README chạy local<br>- Push repo và gửi link cho team review<br>- Thống nhất structure package cho Event service theo module event registration ticket | 26/11/2025 | 26/11/2025 |  |
| 5 | - Thiết kế API spec thô cho Event service theo prefix /api/v1<br>- Hoàn tất endpoint GET /api/v1/events list events theo status và visibility<br>- Thiết kế DTO list và tối ưu response<br>- Fix lỗi Lazy loading do serialize entity bằng cách không trả entity trực tiếp | 27/11/2025 | 27/11/2025 |  |
| 6 | - Phát triển endpoint GET /api/v1/event/{id} trả event detail<br>- Join event_content theo current_version_uuid<br>- Mở rộng GET /api/v1/events với query params q category city country startFrom startTo minPrice maxPrice visibility status page size sort<br>- Chuẩn hóa DTO list và DTO detail | 28/11/2025 | 28/11/2025 |  |
| 7 | - Phát triển endpoints interest uninterest registration theo event id<br>- Xử lý case event không tồn tại deleted full đăng ký trùng<br>- Setup Spring Boot Mail và cấu hình SMTP Gmail<br>- Gửi email xác nhận khi đăng ký thành công<br>- Tách MailService khỏi EventService | 29/11/2025 | 29/11/2025 |  |

---

## ✅ Kết quả đạt được tuần 8
- Port UI sang NextJS.
  - NextJS App Router chạy ổn.
  - Layout nesting và alias import được chuẩn hóa.
  - Các trang Discover Interest Activity chạy được trên local.

- Ổn định repo UI và chia sẻ cho team.
  - UI đạt mức demo theo token OpenSea.
  - Repo được dọn và có README chạy local.

- Khởi tạo Event service và chốt cấu trúc module.
  - Tách module event registration ticket.
  - Tách lớp controller service repository dto config.

- Hoàn thiện các API chính cho Event service.
  - GET /api/v1/events trả danh sách event bằng DTO.
  - GET /api/v1/event/{id} trả event detail theo current_version_uuid.
  - List API hỗ trợ filter bằng query params cơ bản.
  - Không trả JPA entity trực tiếp để tránh lỗi Lazy loading.

- Hoàn thiện tính năng interest và registration.
  - Có endpoints interest uninterest registration.
  - Có kiểm tra event deleted full và đăng ký trùng.

- Hoàn thiện email xác nhận đăng ký.
  - Spring Boot Mail gửi email qua Gmail SMTP.
  - Mail config dùng environment variables.
  - MailService tách khỏi EventService để dễ test và mở rộng.