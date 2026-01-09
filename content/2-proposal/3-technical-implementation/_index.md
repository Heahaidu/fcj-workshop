---
title : "Triển khai kĩ thuật"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

## Tổng quan

Phần triển khai kỹ thuật mô tả cách đưa hệ thống từ môi trường phát triển lên AWS.  
Phần này tập trung vào container hóa dịch vụ, triển khai trên ECS Fargate, cấu hình mạng và bảo mật, và tự động hóa triển khai.

Hệ thống triển khai các thành phần sau:
- Frontend Next.js dạng static trên S3 và CloudFront
- Backend Spring Boot theo microservices trên ECS Fargate
- API Gateway HTTP API kết nối ALB qua VPC Link
- RDS PostgreSQL trong private subnet
- Redis và Kafka chạy nội bộ trên ECS, dùng Cloud Map cho DNS nội bộ
- CloudWatch Logs cho log và giám sát trạng thái dịch vụ

## Giới thiệu

Phần triển khai kỹ thuật đặt các mục tiêu sau:
- Đóng gói ứng dụng nhất quán bằng Docker
- Triển khai dịch vụ theo đơn vị microservice trên ECS Service riêng
- Cách ly hạ tầng bằng VPC và private subnet
- Quản lý thay đổi hạ tầng bằng CloudFormation
- Tự động build và triển khai bằng GitHub Actions và IAM OIDC
- Theo dõi hệ thống bằng health check, log và metric

Phần này trình bày theo các bước thực hiện:
1. Phát triển cục bộ từng service và frontend  
2. Tích hợp toàn hệ thống trên local  
3. Triển khai thủ công lên AWS để xác nhận cấu hình chạy thực tế  
4. Dựng hạ tầng bằng CloudFormation để chuẩn hóa cấu hình  
5. Thiết lập CI/CD để tự động build và triển khai khi cập nhật mã nguồn  

Kết quả đầu ra của phần triển khai gồm:
- Docker image cho từng service và repository trên ECR
- ECS Cluster, Task Definition, ECS Service và Auto Scaling
- ALB và rule định tuyến theo path cho từng service
- API Gateway endpoint đi qua VPC Link vào ALB
- RDS endpoints cho từng database instance
- CloudFront domain cho frontend và S3 buckets cho web và media
- Log tập trung trên CloudWatch Logs