---
title : "Thành phần thiết kế"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

Phần này tả cách hệ thống được chia thành các thành phần (components), vai trò của từng thành phần và cách các thành phần này giao tiếp với nhau để tạo thành một hệ thống hoàn chỉnh.

Hệ thống được thiết kế theo kiến trúc microservices, trong đó mỗi thành phần đảm nhiệm một chức năng nghiệp vụ riêng biệt và được triển khai độc lập.

---

### 1. Tổng quan các thành phần chính

Hệ thống bao gồm các nhóm thành phần sau:

- Presentation Layer (Tầng trình bày)
- API & Routing Layer (Tầng API và định tuyến)
- Service Layer (Tầng dịch vụ nghiệp vụ)
- Data & Messaging Layer (Tầng dữ liệu và messaging)
- Infrastructure Layer (Tầng hạ tầng)

---

### 2. Presentation Layer

**Frontend Web Application**
- Công nghệ: Next.js
- Chức năng:
  - Cung cấp giao diện người dùng cho toàn bộ hệ thống
  - Gửi request API đến backend
- Triển khai:
  - Build dưới dạng static website
  - Lưu trữ trên Amazon S3
  - Phân phối thông qua Amazon CloudFront

Frontend không giao tiếp trực tiếp với các service backend mà thông qua API Gateway.

---

### 3. API & Routing Layer

**API Gateway (HTTP API)**
- Đóng vai trò là điểm vào (entry point) duy nhất cho client bên ngoài
- Chuyển tiếp request từ frontend đến backend thông qua VPC Link
- Giúp che giấu cấu trúc nội bộ của hệ thống

**Application Load Balancer (ALB)**
- Nhận request từ API Gateway
- Thực hiện path-based routing để chuyển request đến đúng microservice
- Kiểm tra health check của các ECS service

Tầng này giúp tách biệt client với các backend service, đồng thời tăng khả năng mở rộng và bảo mật.

---

### 4. Service Layer

Tầng Service bao gồm các microservices, mỗi service đảm nhiệm một nghiệp vụ riêng và chạy độc lập trên Amazon ECS Fargate.

**User Service**
- Quản lý người dùng, thông tin cá nhân và xác thực
- Kết nối đến database riêng
- Sử dụng Redis để cache dữ liệu

**Notification Service**
- Xử lý gửi thông báo (email, notification)
- Lắng nghe sự kiện từ Kafka
- Lưu trữ dữ liệu trên database riêng

**Chatbot Service**
- Xử lý hội thoại và giao tiếp thời gian thực
- Hỗ trợ WebSocket
- Tương tác với Redis và Kafka

**Event Service**
- Quản lý sự kiện và các hoạt động liên quan
- Phát sinh sự kiện bất đồng bộ thông qua Kafka

Mỗi microservice:
- Chạy trong ECS Service riêng
- Có task definition riêng
- Có thể scale độc lập

---

### 5. Data & Messaging Layer

**PostgreSQL (Amazon RDS)**
- Mỗi nhóm nghiệp vụ sử dụng database riêng
- Đảm bảo tách biệt dữ liệu và giảm phụ thuộc giữa các service
- Chỉ truy cập được từ bên trong VPC

**Redis**
- Dùng cho caching và session management
- Được triển khai dưới dạng ECS service nội bộ
- Các service truy cập thông qua Service Discovery

**Apache Kafka**
- Dùng cho giao tiếp bất đồng bộ giữa các microservices
- Hỗ trợ event-driven architecture
- Giúp giảm coupling giữa các service

---

### 6. Infrastructure Layer

**Amazon ECS Fargate**
- Chạy container mà không cần quản lý server
- Mỗi component backend là một ECS service

**Amazon VPC**
- Cung cấp môi trường mạng riêng biệt
- Phân tách public subnet và private subnet
- Đảm bảo các service nội bộ không bị truy cập trực tiếp từ internet

**IAM**
- Quản lý quyền truy cập giữa các component
- ECS Task Role cho phép service truy cập S3 và các tài nguyên cần thiết
- GitHub Deploy Role dùng cho CI/CD thông qua OIDC

**CloudFormation**
- Quản lý toàn bộ hạ tầng dưới dạng mã
- Đảm bảo tính nhất quán giữa các môi trường triển khai

---

### 7. Giao tiếp giữa các thành phần

- Frontend → API Gateway: HTTP/HTTPS
- API Gateway → ALB → ECS Services: HTTP
- Service → Service (bất đồng bộ): Kafka
- Service → Redis: TCP
- Service → Database: TCP (PostgreSQL)

Thiết kế này giúp hệ thống dễ mở rộng, dễ bảo trì và phù hợp với môi trường cloud-native.