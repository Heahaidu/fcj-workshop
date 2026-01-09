---
title : "Khả năng mở rộng"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 3.5 </b> "
---

Phần này mô tả cách hệ thống được thiết kế để có thể mở rộng linh hoạt nhằm đáp ứng sự gia tăng về số lượng người dùng, lưu lượng truy cập và khối lượng xử lý trong tương lai.

Hệ thống áp dụng mô hình **horizontal scaling** kết hợp với kiến trúc microservices để đảm bảo khả năng mở rộng độc lập cho từng thành phần.

---

### 1. Mở rộng tầng trình bày (Presentation Layer)

- Frontend được build dưới dạng static website và phân phối thông qua **Amazon CloudFront**.
- CloudFront tự động mở rộng để phục vụ lượng lớn người dùng đồng thời.
- Việc sử dụng CDN giúp:
  - Giảm tải cho backend
  - Giảm độ trễ truy cập từ các khu vực địa lý khác nhau

Tầng frontend không cần quản lý cơ chế scaling thủ công.

---

### 2. Mở rộng tầng API và định tuyến

- **API Gateway HTTP API** có khả năng mở rộng tự động theo lưu lượng request.
- Application Load Balancer:
  - Phân phối request đến nhiều ECS task
  - Hỗ trợ path-based routing cho nhiều microservices
- Health check của ALB đảm bảo chỉ các task đang hoạt động mới nhận request.

Tầng API không trở thành điểm nghẽn khi lưu lượng tăng cao.

---

### 3. Mở rộng tầng dịch vụ (Service Layer)

- Mỗi microservice được triển khai dưới dạng **ECS Service riêng**.
- ECS Fargate hỗ trợ:
  - Tự động scale số lượng task theo tải
  - Mở rộng độc lập từng service

- **Auto Scaling** được cấu hình dựa trên:
  - CPU utilization
  - Desired task count tối thiểu và tối đa

Cách tiếp cận này cho phép:
- Scale User Service mà không ảnh hưởng đến Event Service
- Scale Chatbot Service khi có nhiều kết nối đồng thời

---

### 4. Mở rộng tầng dữ liệu (Data Layer)

**Database (Amazon RDS PostgreSQL)**
- Mỗi nhóm nghiệp vụ sử dụng database riêng, giảm tranh chấp tài nguyên.
- Có thể:
  - Scale up instance khi cần
  - Bật Multi-AZ để tăng khả năng chịu lỗi

**Redis**
- Giảm tải cho database bằng caching
- Tăng hiệu suất truy vấn dữ liệu thường xuyên

---

### 5. Mở rộng giao tiếp bất đồng bộ

- **Apache Kafka** được sử dụng cho giao tiếp bất đồng bộ giữa các microservices.
- Các service có thể:
  - Publish và consume message độc lập
  - Xử lý tải cao mà không làm chậm luồng request chính

Kafka giúp hệ thống:
- Hấp thụ spike traffic
- Tránh coupling chặt giữa các service

---

### 6. Tối ưu khả năng mở rộng bằng thiết kế ứng dụng

- Backend services được thiết kế theo mô hình **stateless**.
- Session và cache được lưu trữ tập trung trên Redis.
- Không lưu trạng thái người dùng trong container.

Thiết kế này cho phép ECS task scale ngang mà không ảnh hưởng đến logic ứng dụng.

---

### 7. Tổng kết

Thiết kế Scalability của hệ thống đảm bảo:

- Mở rộng tự động ở nhiều tầng (Frontend, API, Service)
- Scale độc lập từng microservice
- Giảm điểm nghẽn bằng caching và messaging
- Phù hợp với kiến trúc cloud-native và microservices

Hệ thống có thể đáp ứng nhu cầu tăng trưởng người dùng và lưu lượng truy cập trong tương lai mà không cần thay đổi lớn về kiến trúc.