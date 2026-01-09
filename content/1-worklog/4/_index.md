---
title : "Worklog tuần 4"
date :  "`r Sys.Date()`" 
weight : 4
pre: <b> 1.4 </b>
chapter : false
---

## 📍 Mục tiêu tuần 4
- Share database ERD và thống nhất quy ước với team.
- Map API theo database và chuẩn hóa luồng versioning cho event_content.
- Phác thảo UI trên Figma và chuẩn hóa component.
- Triển khai ứng dụng trên ECS theo workshop.
- Thiết lập CI/CD cho ECS theo tag trigger.
- Nắm khái niệm AWS Security Hub và các standards.
- Thực hành VPC Peering và kiểm tra DNS qua peering.
- Thực hành AWS Transit Gateway kết nối nhiều VPC.
- Thống nhất kiến trúc microservice và nguyên tắc database per service.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Share database ERD và notes quy ước naming status visibility versioning<br>- Map API User Auth Event Registration theo DB mới<br>- Xác định bảng event và sửa event theo versioning event_content<br>- Phác thảo UI/UX trên Figma | 27/10/2025 | 27/10/2025 | [Project Proposal](https://github.com/AWS-First-Cloud-Journey/Internship/tree/master/project-proposal) |
| 3 | - Tìm hiểu và thực hành triển khia ứng dụng trên ECS | 28/10/2025 | 28/10/2025 | [FCJ Workshop 16](https://000016.awsstudygroup.com/vi/)<br>[FCJ Workshop 15](https://000015.awsstudygroup.com/vi/) |
| 4 | - Triển khai CI/CD Với ECS | 29/10/2025 | 29/10/2025 | [FCJ Workshop 17](https://000017.awsstudygroup.com/vi/)<br>[AWS CodeBuild Docs](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html)<br>[Amazon ECR Docs](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) |
| 5 | - Nghiên cứu AWS Security Hub theo workshop và docs<br> | 30/10/2025 | 30/10/2025 | [FCJ Workshop 18](https://000018.awsstudygroup.com/vi/)<br>[Security Hub Docs](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html) |
| 6 | - Tìm hiểu và thực hành triển khai VPC Peering<br>- Chỉnh UI đồ án trên Figma | 31/10/2025 | 31/10/2025 | [FCJ Workshop 19](https://000019.awsstudygroup.com/vi/)<br>[VPC Peering Docs](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)<br>[VPC Peering Routing](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-routing.html)|
| 7 | - Tìm hiểu và thực hành triển khai Transit Gateway<br>- Tiếp tục chỉnh UI App Page trên Figma và đổi cách tổ chức layout component | 01/11/2025 | 01/11/2025 | [FCJ Workshop 20](https://000020.awsstudygroup.com/vi/) |
| CN | - Họp nhóm và thống nhất danh sách microservice và nhiệm vụ<br>- Gộp User service và Page service<br>- Xác định Event service phát sự kiện qua Kafka<br>- Xác định Notification service nhận sự kiện và gửi email thông báo<br>- Áp dụng database per service cho Postgres<br>- Phác thảo nhóm bảng theo từng service | 02/11/2025 | 02/11/2025 |  |

---

## ✅ Kết quả đạt được tuần 4
- Share database ERD và notes cho team.
  - Thống nhất naming status visibility versioning.
  - Map API theo database và chốt luồng tạo sửa event theo event_content.

- Triển khai ứng dụng trên ECS.
  - Tạo private subnets NAT Gateway route table.
  - Dùng Cloud Map để FE gọi BE theo DNS nội bộ.
  - Dùng ALB target type IP để route tới Fargate tasks.
  - Chạy backend theo rolling update.

- Thiết lập CI/CD cho ECS.
  - Dùng GitHub Actions theo tag trigger để build push ECR và cập nhật ECS.
  - Dùng CodeBuild theo tag trigger và lưu PAT trong Secrets Manager.
  - Kiểm tra ECR tags task definition revisions service deployments và thay đổi hiển thị trên website.

- Nghiên cứu Security Hub.
  - Nắm mục tiêu và cách đọc results theo standards.
  - Nắm AWS FSBP CIS PCI DSS.

- Hoàn thành lab VPC Peering.
  - Tạo peering và cấu hình route table hai chiều.
  - Bật Cross Peering DNS và kiểm tra theo Public DNS.

- Hoàn thành lab Transit Gateway.
  - Tạo TGW route table và điều khiển association propagation.
  - Kết nối 4 VPC theo mô hình hub and spoke.

- Thống nhất kiến trúc microservice.
  - Xác định 4 service và trách nhiệm chính.
  - Áp dụng database per service.
  - Phác thảo nhóm bảng theo từng service và hướng dữ liệu vé QR.