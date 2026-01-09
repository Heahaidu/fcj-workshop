---
title : "Worklog tuần 5"
date :  "`r Sys.Date()`" 
weight : 5
pre: <b> 1.5 </b>
chapter : false
---

## 📍 Mục tiêu tuần 5
- Nghiên cứu kiến trúc microservice và Spring Boot framework.
- Thiết kế database chi tiết cho User Page Event Notification service.
- Rà soát và tối ưu schema với index và ràng buộc.
- Triển khai AWS CodePipeline.
- Triển khai AWS Storage Gateway.
- Chạy local test migration và xử lý lỗi extension.
- Họp nhóm xác nhận database và kiến trúc.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Nghiên cứu microservice và Spring Boot<br>- Tìm hiểu JPA entity mapping và REST API<br>- Tìm hiểu Kafka producer consumer trong Spring Boot | 03/11/2025 | 03/11/2025 | [Tutorial](https://www.youtube.com/playlist?list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c) |
| 3 | - Tổng hợp tài liệu microservice database per service Spring Boot Kafka<br>- Thiết kế schema User Page service<br>- Thiết kế schema Event service | 04/11/2025 | 04/11/2025 | |
| 4 | - Thêm index cho các trường truy vấn chính cho database<br>- Rà soát schema theo naming kiểu dữ liệu ràng buộc default value<br>- Hoàn thiện schema Notification service | 05/11/2025 | 05/11/2025 |  |
| 5 | - Triển khai AWS CodePipeline gồm Source Build Deploy | 06/11/2025 | 06/11/2025 | [FCJ Workshop 23](https://000023.awsstudygroup.com/vi/)<br>[CodePipeline Docs](https://docs.aws.amazon.com/codepipeline/) |
| 6 | - Triển khai AWS Storage Gateway File Gateway<br>- Rà soát schema theo service và checklist index<br>- Chạy local migration và tạo bảng<br>- Sửa lỗi uuid-ossp bằng pgcrypto và tạo hàm uuid_generate_v7 | 07/11/2025 | 07/11/2025 | [FCJ Workshop 24](https://000024.awsstudygroup.com/vi/)<br>[PostgreSQL pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) |
| CN | - Họp nhóm review database theo từng service<br>- Thống nhất kiến trúc microservice gồm User Page Event Notification AIChat<br>- Thống nhất database per service cho Postgres<br>- Thống nhất giao tiếp qua Kafka từ Event sang Notification và AIChat<br>- Thống nhất stack NextJS Tailwind Spring Boot và hạ tầng AWS | 09/11/2025 | 09/11/2025 |  |

---

## ✅ Kết quả đạt được tuần 5
- Hoàn thành nghiên cứu microservice và Spring Boot.
  - Nắm JPA entity mapping REST controller service layer.
  - Nắm Kafka producer consumer và message giữa service.

- Hoàn thành schema cho User Page service.
  - Có bảng account user_profile admin_profile account_audit_log.
  - Có ràng buộc và index cho truy vấn chính.

- Hoàn thành schema cho Event service.
  - Có bảng event event_content event_registration event_feedback.
  - Có versioning cho event_content.
  - Dùng UUID để tham chiếu giữa service.

- Hoàn thành schema cho Notification service.
  - Có cấu trúc lưu notification và lịch sử gửi email.

- Rà soát schema và tối ưu truy vấn.
  - Thêm index cho email event_id user_id status và các bảng cần filter nhiều.

- Chạy local migration và xử lý lỗi UUID extension.
  - Thay uuid-ossp bằng pgcrypto.
  - Tạo hàm uuid_generate_v7 và dùng làm default.

- Triển khai AWS CodePipeline.
  - Pipeline chạy qua Source Build Deploy.
  - Deploy ứng dụng lên EC2 bằng CodeDeploy Agent.

- Triển khai AWS Storage Gateway.
  - SMB File Share hoạt động.
  - Dữ liệu đồng bộ từ Windows lên S3.

- Thống nhất kiến trúc và phân công trong nhóm.
  - Thống nhất 4 service và trách nhiệm.
  - Thống nhất công nghệ và dịch vụ AWS dự kiến dùng.