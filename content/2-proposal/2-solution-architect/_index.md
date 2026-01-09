---
title : "Kiến trúc giải pháp"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---

![Solution-Architecture](/2.architect/Solution-Architecture.jpg)


## 2.1. Tổng quan kiến trúc

Hệ thống được xây dựng theo kiến trúc cloud-native microservices trên AWS. Các thành phần được container hóa và triển khai trên Amazon ECS Fargate.

Đường đi xử lý chính:

1. Trình duyệt tải giao diện từ CloudFront. CloudFront đọc nội dung từ S3 WebBucket.
2. Trình duyệt gọi API tới API Gateway (HTTP API).
3. API Gateway chuyển request qua VPC Link tới Application Load Balancer.
4. ALB định tuyến theo URL path tới từng microservice trên ECS Fargate.
5. Microservice đọc ghi dữ liệu trên RDS for PostgreSQL.
6. Microservice dùng Redis cho cache và Kafka cho message streaming khi cần.
7. Microservice lưu media lên S3 ImagesBucket.
8. Container ghi log vào CloudWatch Logs.

---

## 2.2. Các dịch vụ AWS sử dụng

<!-- ![Infrastructure Composer](/2.architect/infrastructure_composer.png) -->

- **Amazon VPC**: Mạng riêng cho toàn hệ thống. 2 Availability Zones. Public subnet và private subnet.
- **Internet Gateway**: Kết nối Internet cho tài nguyên public.
- **NAT Gateway (2)**: Kết nối Internet outbound cho tài nguyên trong private subnet.
- **Amazon S3**:
  - WebBucket: Lưu frontend dạng static.
  - ImagesBucket: Lưu object media. Bật CORS cho upload và download.
- **Amazon CloudFront**: CDN cho WebBucket. Dùng Origin Access Control để đọc S3 private.
- **Amazon API Gateway (HTTP API)**: Public endpoint cho API. Proxy vào ALB qua VPC Link.
- **Application Load Balancer**: Nhận request HTTP port 80. Route theo path tới ECS services.
- **Amazon ECS on Fargate**:
  - 4 microservices: user, notification, chatbot, event.
  - 2 service nội bộ: Redis, Kafka.
- **AWS Cloud Map (Service Discovery)**: Cấp DNS nội bộ cho Redis và Kafka trong VPC.
- **Amazon RDS for PostgreSQL**: 3 DB instances trong private subnets. Bật encryption và backup.
- **AWS IAM**:
  - ECS execution role cho ECS agent.
  - ECS task role cho quyền ứng dụng.
  - GitHub OIDC provider và GitHub deploy role cho CI/CD.
- **Amazon CloudWatch Logs**: Lưu log ECS. Retention 14 ngày.
- **Application Auto Scaling**: Scale ECS service theo CPU.

---

## 2.3. Thiết kế các thành phần hệ thống

### 2.3.1 CI/CD Pipeline

Source code được quản lý trên GitHub.  
GitHub Actions thực hiện build và deploy tự động.

Pipeline backend bao gồm:

- Build ứng dụng Spring Boot  
- Build Docker image  
- Push image lên Amazon ECR  
- Cập nhật ECS Task Definition  
- Redeploy ECS Service  

Pipeline frontend build ứng dụng NextJS và deploy static files lên S3.  
CloudFront được invalidate sau mỗi lần deploy.

CI/CD sử dụng IAM Role với GitHub OIDC.  
Không sử dụng access key tĩnh.

| ![GitHub Actions workflow Service](/2.architect/gitAction_service.png) | ![GitHub Actions workflow Frontend](/2.architect/gitAction_web.png) |
| :----------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------: |
|                           _Hình 1. Code file GitHub Actions workflow cho service_                            |                         _Hình 2. Code file GitHub Actions workflow cho front-end_                         |

### 2.3.2 Frontend

Frontend được build dưới dạng static website.  
File được lưu trữ trên Amazon S3.

CloudFront phân phối nội dung đến người dùng.  
Origin Access Control giới hạn quyền truy cập vào S3.  
Người dùng không truy cập trực tiếp vào bucket.

![Hình S3:](/2.architect/s3.png)

### 2.3.3 Backend

Backend gồm nhiều microservices viết bằng Spring Boot.  
Mỗi service chạy trong một ECS Service riêng biệt.

Các container chạy trong private subnet.  
Service chỉ nhận traffic từ Application Load Balancer.

ALB sử dụng path-based routing để định tuyến request.  
Mỗi microservice có Target Group riêng.

![Các image được lưu ở ECR](/2.architect/ecr.png)

### 2.3.4 Database

Hệ thống sử dụng Amazon RDS PostgreSQL.  
Mỗi nhóm chức năng sử dụng một database instance riêng.

RDS được triển khai trong private subnet.  
Chỉ ECS Service được phép kết nối thông qua Security Group.

Mô hình database-per-service giảm phụ thuộc giữa các microservices.

[Video tạo database RDS](video/02-Solution-Architecture/create_rds_eventdb.mp4)

[Video kiểm tra kết nối RDS trên local](video/02-Solution-Architecture/connect_rds.mp4)

### 2.3.5 Dịch vụ nội bộ

Hệ thống sử dụng các service nội bộ sau:

- **Redis**: Caching và session management  
- **Apache Kafka**: Giao tiếp bất đồng bộ giữa các microservices  

Redis và Kafka chạy trên ECS Fargate.  
Service Discovery được triển khai bằng AWS Cloud Map.  
Các service kết nối thông qua DNS nội bộ trong VPC.

---

## 2.4. Kiến trúc bảo mật

Hệ thống áp dụng bảo mật nhiều lớp:

- Phân tách public subnet và private subnet  
- ECS Task và RDS không có public IP  
- Security Group kiểm soát luồng truy cập  
- IAM Role áp dụng nguyên tắc phân quyền tối thiểu  
- CloudFront hạn chế truy cập trực tiếp vào S3  
- API Gateway cách ly client khỏi backend  

Tất cả truy cập nội bộ diễn ra trong mạng riêng.

---

## 2.5. Thiết kế khả năng mở rộng

Hệ thống hỗ trợ mở rộng linh hoạt:

- ECS Service tự động scale theo CPU utilization  
- Mỗi microservice scale độc lập  
- ALB phân phối đều lưu lượng truy cập  
- Kiến trúc triển khai trên hai Availability Zone  
- CI/CD cho phép cập nhật phiên bản không gián đoạn dịch vụ  

Thiết kế phù hợp với hệ thống có lưu lượng biến động lớn.