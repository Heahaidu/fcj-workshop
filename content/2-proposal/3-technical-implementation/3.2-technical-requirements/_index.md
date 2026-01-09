---
title : "Yêu cầu kĩ thuật"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

Phần này mô tả các yêu cầu kỹ thuật chính để triển khai và vận hành hệ thống theo kiến trúc đã đề xuất.

---

### Ngôn ngữ và Framework

- **Backend**  
  - Java  
  - Spring Boot  
  - Kiến trúc microservices  

- **Frontend**  
  - Next.js (React-based framework)  
  - Build dưới dạng static website (SPA)  
  - Dùng chung cho toàn bộ hệ thống  

---

### Công nghệ và Giao thức

- **Containerization**  
  - Docker  
  - Docker Compose (phục vụ phát triển và test local)

- **Giao tiếp**  
  - RESTful API qua HTTP/HTTPS  
  - WebSocket (phục vụ Chatbot Service)

- **Giao tiếp nội bộ**  
  - Redis cho caching và session management  
  - Apache Kafka cho giao tiếp bất đồng bộ giữa các microservices  

---

### Hạ tầng Cloud

- **Compute & Orchestration**  
  - Amazon ECS với Fargate launch type  
  - Mỗi microservice chạy trong một ECS Service riêng  

- **Networking**  
  - Amazon VPC với public và private subnet trên hai Availability Zone  
  - Application Load Balancer dùng path-based routing  
  - API Gateway HTTP API kết nối ALB thông qua VPC Link  

- **Storage & Database**  
  - Amazon RDS PostgreSQL cho dữ liệu nghiệp vụ  
  - Amazon S3 cho frontend static website và lưu trữ hình ảnh  
  - Amazon CloudFront làm CDN cho frontend  

---

### Infrastructure as Code

- **AWS CloudFormation**  
  - Mô tả toàn bộ hạ tầng dưới dạng mã  
  - Cho phép tái sử dụng template cho nhiều môi trường  
  - Giảm lỗi cấu hình thủ công và đảm bảo tính nhất quán  

---

### CI/CD và Tự động hóa

- **Source Control**  
  - Git  
  - GitHub  

- **CI/CD Pipeline**  
  - GitHub Actions  
  - Sử dụng GitHub OIDC để assume IAM Role trên AWS  
  - Tự động build và push Docker image lên Amazon ECR  
  - Tự động deploy backend lên ECS  
  - Tự động deploy frontend lên S3 và invalidate CloudFront cache  

---

### Logging và Monitoring

- **Logging**  
  - Amazon CloudWatch Logs cho ECS services  

- **Monitoring**  
  - Theo dõi trạng thái ECS service và health check của ALB  
  - Giám sát auto scaling dựa trên CPU utilization  

---

### Môi trường triển khai

- **Local Development Environment**  
  - Phát triển và test từng microservice độc lập  
  - Chạy toàn bộ hệ thống bằng Docker Compose  

- **Cloud Environment**  
  - Triển khai trên AWS  
  - Hạ tầng được quản lý và version hóa bằng CloudFormation  

---