---
title : "Worklog tuần 11"
date :  "`r Sys.Date()`" 
weight : 11
pre: <b> 1.11 </b>
chapter : false
---

## 📍 Mục tiêu tuần 11
- Deploy FE lên S3 và phân phối qua CloudFront.
- Tạo nhanh S3 CloudFront IAM Role bằng CloudFormation.
- Tạo pipeline GitHub Actions deploy FE lên S3 bằng IAM Role.
- Tích hợp upload ảnh lên S3 từ event-service.
- Sửa endpoint create event để nhận multipart và lưu image_urls.
- Đồng bộ FE và BE cho create event có ảnh.
- Thiết kế kiến trúc triển khai BE trên AWS.
- Setup thủ công VPC subnets ECS ALB RDS để kiểm thử.
- Chuẩn hóa danh sách env vars cho các service.
- Viết CloudFormation bản đầu cho network và RDS.
- Mở rộng CloudFormation cho ECS ALB ECR và security groups.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 3 | - Viết CloudFormation tạo S3 bucket FE và CloudFront distribution<br>- Cấu hình origin và default root object cho SPA<br>- Tạo IAM Role cho CI deploy<br>- Tạo GitHub Actions build FE và sync artifacts lên S3<br>- Assume role từ GitHub Actions và chạy invalidate CloudFront<br>- Fix lỗi trust policy và policy S3 do role cấu hình sai<br>- Fix lỗi build FE theo log CI | 16/12/2025 | 16/12/2025 |  |
| 4 | - Tạo S3 bucket lưu ảnh event<br>- Tạo ImageStorageService dùng AWS SDK for Java v2<br>- Upload ảnh và trả object key hoặc URL<br>- Lưu image_urls vào event_content<br>- Sửa controller nhận multipart gồm JSON và image<br>- Đồng bộ FE và BE cho create event có ảnh | 17/12/2025 | 17/12/2025 |  |
| 5 | - Thiết kế kiến trúc triển khai BE trên AWS theo VPC subnets ECS ALB RDS<br>- Setup thủ công môi trường để kiểm thử và debug<br>- Ghi danh sách resource và dependency để đưa vào CloudFormation<br>- Liệt kê env vars cho DB Kafka mail S3 JWT base url CORS profile | 18/12/2025 | 18/12/2025 |  |
| 6 | - Rà ECS service task definition port mapping<br>- Rà target group health check<br>- Rà RDS endpoint và security group inbound<br>- Tách CloudFormation theo module network database compute ingress<br>- Xác định parameters và outputs cần cho debug | 19/12/2025 | 19/12/2025 |  |
| 7 | - Hoàn thiện baseline môi trường BE chạy được mức cơ bản<br>- Viết CloudFormation bản đầu cho VPC subnets IGW route table<br>- Viết CloudFormation cho RDS và security group cơ bản<br>- Xuất outputs ALB DNS RDS endpoint VPC id | 20/12/2025 | 20/12/2025 |  |
| CN | - Thêm ALB listener và target group HTTP vào CloudFormation<br>- Thêm ECS cluster task definition skeleton service<br>- Tạo security group cho ALB và ECS<br>- Chuẩn hóa env vars và mapping vào task definition | 21/12/2025 | 21/12/2025 |  |

---

## ✅ Kết quả đạt được tuần 11
- Deploy FE thành công trên AWS.
  - FE chạy qua CloudFront domain.
  - Deploy tự động bằng GitHub Actions sync lên S3.
  - Cache invalidation chạy được sau deploy.

- Hoàn thiện cấu hình IAM Role cho CI.
  - Sửa trust policy để khớp repo.
  - Sửa permission policy cho s3:PutObject và s3:ListBucket.
  - Build lỗi trên CI được xử lý theo log.

- Hoàn thiện upload ảnh cho event-service.
  - event-service upload ảnh lên S3 bằng AWS SDK.
  - Create event nhận multipart và lưu image_urls vào event_content.
  - FE tạo event kèm ảnh thành công và event detail trả đúng ảnh.

- Bắt đầu chuẩn hóa triển khai BE trên AWS.
  - Có baseline setup thủ công gồm VPC ECS ALB RDS để kiểm thử.
  - Có danh sách resource dependency và điểm dễ sai gồm port health check env vars.

- Bắt đầu viết CloudFormation cho môi trường BE.
  - Template network và RDS chạy được mức tạo resource chính.
  - Template có outputs phục vụ debug và tích hợp.
  - Template được mở rộng thêm ECS và ALB ở mức skeleton.

- Chuẩn hóa env vars cho các service.
  - Có danh sách env vars theo nhóm DB Kafka S3 mail JWT base url CORS profile.
  - Có kế hoạch gắn vào task definitions khi deploy ECS.