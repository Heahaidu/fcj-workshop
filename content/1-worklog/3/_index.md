---
title : "Worklog tuần 3"
date :  "`r Sys.Date()`" 
weight : 3
pre: <b> 1.3 </b>
chapter : false
---

## 📍 Mục tiêu tuần 3
- Hiểu yêu cầu project-proposal và xác định phạm vi triển khai.
- Thống nhất ý tưởng sản phẩm và phân công trong nhóm.
- Thiết kế và chuẩn hóa CSDL cho các module chính.
- Xây danh sách API cho User Auth Event Registration.
- Thực hành triển khai ứng dụng bằng Docker trên AWS.
- Chốt mô hình versioning cho nội dung event.
- Thêm audit log cho thao tác tài khoản.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Đọc yêu cầu project-proposal<br>- Họp nhóm để đề xuất ý tưởng<br>- Thiết kế ERD bản thô | 20/10/2025 | 20/10/2025 | [Project Proposal](https://github.com/AWS-First-Cloud-Journey/Internship/tree/master/project-proposal) |
| 3 | - Chuẩn hóa ERD phần User theo naming PK FK constraints<br>- Lập danh sách API User Auth và mô tả chức năng | 21/10/2025 | 21/10/2025 |  |
| 4 | - Lập danh sách API Event và mô tả chức năng<br>- Nhóm API theo CRUD ẩn hiện tham gia lưu quan tâm đánh giá | 22/10/2025 | 22/10/2025 |  |
| 5 | - Lập danh sách API Registration Attendee và mô tả chức năng<br>- Xác định trạng thái tham gia và quyền user host<br>- Cập nhật API list User và Event vào project-proposal | 23/10/2025 | 23/10/2025 | |
| 6 | - Thực hành triển khai ứng dụng bằng Docker trên AWS | 24/10/2025 | 24/10/2025 | [FCJ Workshop 15](https://000015.awsstudygroup.com/vi) |
| 7 | - Họp nhóm thống nhất ý tưởng sản phẩm và phân công<br>- Thống nhất công nghệ (NextJS, Spring Boot, Postgres)<br>- Phác thảo kiến trúc triển khai AWS ở mức thô<br>- Thiết kế lại database bản thô | 25/10/2025 | 25/10/2025 | |
| CN | - Hoàn thiện database bản thô để triển khai backend<br> | 26/10/2025 | 26/10/2025 | |

---

## ✅ Kết quả đạt được tuần 3
- Hoàn thành tài liệu định hướng cho project-proposal.
  - Xác định phạm vi và đầu việc theo tiêu chí đề bài.
  - Tổng hợp điểm cần làm rõ trong buổi họp tiếp theo.

- Hoàn thành tài liệu API ở mức proposal.
  - Có danh sách API User Auth.
  - Có danh sách API Event.
  - Có danh sách API Registration Attendee.
  - Đưa API list vào project-proposal.

- Hoàn thành bản thiết kế database bản thô.
  - Chuẩn hóa naming PK FK constraints cho phần User.
  - Thêm account_audit_log cho lịch sử thao tác tài khoản.
  - Tách event và event_content để lưu phiên bản nội dung event.
  - Xác định quy tắc cập nhật version_number previous_version_uuid is_current_version.

- Triển khai ứng dụng bằng Docker trên AWS.
  - Tạo VPC và security groups.
  - Tạo RDS MySQL và import dữ liệu.
  - Tạo EC2 Ubuntu và cài Docker.
  - Chạy ứng dụng bằng Docker và docker compose.
  - Push images lên ECR và Docker Hub.

- Thống nhất định hướng nhóm.
  - Thống nhất ý tưởng sản phẩm và phân công.
  - Thống nhất stack NextJS Tailwind Spring Boot Postgres.
  - Liệt kê dịch vụ AWS dự kiến dùng theo module.