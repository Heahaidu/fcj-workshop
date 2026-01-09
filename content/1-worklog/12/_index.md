---
title : "Worklog tuần 12"
date :  "`r Sys.Date()`" 
weight : 12
pre: <b> 1.12 </b>
chapter : false
---

## 📍 Mục tiêu tuần 12
- Deploy môi trường BE bằng CloudFormation.
- Push image lên ECR và chạy ECS tasks.
- Debug lỗi ALB unhealthy và task restart.
- Fix DB connection cho các service trên ECS.
- Chọn migration strategy và xử lý vấn đề Flyway.
- Bổ sung health endpoint và ổn định health check cho ALB.
- Fix FE gọi BE qua HTTPS và xử lý SPA routing trên CloudFront.
- Kiểm thử luồng chính và tạo backlog bug để phân công.
- Viết proposal báo cáo và tổng hợp kiến trúc triển khai.
- Chuẩn hóa checklist deploy rollback.
- Hoàn thiện worklog và chuẩn hóa kế hoạch theo backlog.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Deploy CloudFormation stack và tạo tài nguyên môi trường<br>- Tạo ECR repos theo service và push image<br>- Debug ALB unhealthy và ECS task restart theo target group events và container logs<br>- Rà env vars DB endpoint và credentials. đồng bộ env vars cho các service | 22/12/2025 | 22/12/2025 |  |
| 3 | - Fix DB connection để dừng restart loop<br>- Tích hợp Flyway và debug cho service | 23/12/2025 | 23/12/2025 |  |
| 4 | - Fix lỗi ALB health check<br>- Rà listener rules ports port mapping và security groups<br>- Xác nhận ALB route được tới service | 24/12/2025 | 24/12/2025 |  |
| 5 | - Fix mixed content do FE HTTPS gọi BE HTTP bằng HTTPS endpoint tạm qua API Gateway<br>- Cập nhật FE base API URL sang HTTPS<br>- Fix SPA routing bằng CloudFront Function rewrite về index.html | 25/12/2025 | 25/12/2025 |  |
| 6 | - Kiểm thử các luồng login register list events event detail create event registration notification<br>- Ghi bug theo bước tái hiện expected result và mức độ<br>- Tách lỗi FE và BE theo log và status code<br>- Chỉnh UI login register popup gồm layout validation loading error | 26/12/2025 | 26/12/2025 |  |
| 7 | - Viết proposal báo cáo gồm mục tiêu phạm vi kiến trúc FE BE CI deploy và rủi ro<br>- Chuẩn hóa checklist deploy rollback theo tầng CloudFormation ECR ECS ALB DB | 27/12/2025 | 27/12/2025 |  |
| CN | - Hoàn thiện workshop| 28/12/2025 | 28/12/2025 |  |

---

## ✅ Kết quả đạt được tuần 12
- Deploy môi trường BE và xác định lỗi gốc.
  - CloudFormation stack chạy được và image push lên ECR thành công.
  - ECS task restart loop và ALB unhealthy được truy ra từ log.
  - Nguyên nhân chính nằm ở DB config và thiếu env vars.

- Fix DB connection để service không crash lúc start.
  - Service kết nối RDS sau khi dùng Hibernate ddl-auto update.
  - Flyway chưa chạy được và được đưa vào backlog để xử lý sau.

- Ổn định ALB health check và ECS tasks.
  - Bật Spring Boot actuator health và probes.
  - Target group chuyển healthy và task ổn định hơn.
  - ALB routing được xác nhận theo listener rules và port mapping.

- Fix FE deploy issues khi kết nối BE.
  - Mixed content được xử lý bằng HTTPS endpoint tạm qua API Gateway.
  - SPA routing được xử lý bằng CloudFront Function rewrite về index.html.
  - Deep link và refresh route hoạt động không cần .html.

- Bắt đầu giai đoạn test hệ thống sau deploy.
  - Có backlog bug theo luồng và mức độ.
  - UI login register popup được chỉnh để giảm lỗi thao tác.
  - Có kế hoạch phân công bug theo module.

- Hoàn thiện tài liệu dự án.
  - Proposal có bản nháp với kiến trúc và quyết định kỹ thuật.
  - Có checklist deploy rollback theo tầng.
  - Worklog được chuẩn hóa theo timeline và bổ sung root cause solution lesson prevention.