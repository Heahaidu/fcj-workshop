---
title : "Kiến trúc bảo mật"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.4 </b> "
---

Phần Security Architecture mô tả các cơ chế bảo mật được áp dụng nhằm đảm bảo tính bảo mật, toàn vẹn và sẵn sàng của hệ thống trong suốt vòng đời vận hành.

Kiến trúc bảo mật được thiết kế theo nguyên tắc **defense in depth**, kết hợp nhiều lớp bảo vệ từ tầng mạng, ứng dụng đến hạ tầng và quy trình CI/CD.

---

### 1. Bảo mật tầng mạng (Network Security)

- Hệ thống được triển khai trong **Amazon VPC** riêng biệt.
- VPC được chia thành:
  - **Public Subnet**: chứa Application Load Balancer và NAT Gateway.
  - **Private Subnet**: chứa ECS services, RDS, Redis và Kafka.

- Các backend services **không có public IP** và không thể truy cập trực tiếp từ internet.

- **Security Groups** được cấu hình theo nguyên tắc *least privilege*:
  - ALB chỉ mở cổng HTTP/HTTPS từ API Gateway.
  - ECS services chỉ nhận traffic từ ALB.
  - RDS, Redis và Kafka chỉ cho phép truy cập từ các ECS services trong VPC.

Thiết kế này giúp cô lập các thành phần quan trọng và giảm bề mặt tấn công.

---

### 2. Bảo mật tầng truy cập (Access Control)

- **API Gateway** đóng vai trò là cổng truy cập duy nhất từ bên ngoài.
- Mọi request từ client đều phải đi qua API Gateway trước khi vào hệ thống backend.
- Path-based routing trên ALB đảm bảo request chỉ được chuyển đến đúng service.

- Các endpoint nội bộ (internal service, database, cache) không được public expose.

---

### 3. Bảo mật ứng dụng (Application Security)

- Backend services được thiết kế theo mô hình **stateless**, giảm rủi ro liên quan đến session.
- Các service chỉ truy cập database và tài nguyên cần thiết cho nghiệp vụ của mình.
- Giao tiếp giữa các service được kiểm soát thông qua:
  - RESTful API (đồng bộ)
  - Kafka (bất đồng bộ)

- Health check endpoint được giới hạn chức năng, không trả về thông tin nhạy cảm.

---

### 4. Bảo mật dữ liệu (Data Security)

- **Dữ liệu trong quá trình truyền (Data in Transit)**:
  - Sử dụng HTTPS cho giao tiếp giữa client và CloudFront/API Gateway.
  - Giao tiếp nội bộ trong VPC được kiểm soát bằng Security Group.

- **Dữ liệu lưu trữ (Data at Rest)**:
  - Amazon RDS sử dụng cơ chế mã hóa dữ liệu.
  - Amazon S3 bật mã hóa cho dữ liệu frontend và file upload.
  - Thông tin nhạy cảm (mật khẩu database) không hard-code trong source code.

- Database chỉ cho phép kết nối từ các ECS service đã được cấp quyền.

---

### 5. Quản lý danh tính và phân quyền (IAM)

- **IAM Role cho ECS Task**:
  - Mỗi ECS task sử dụng một IAM Role riêng.
  - Role chỉ cấp quyền tối thiểu cần thiết (ví dụ: truy cập S3, CloudWatch Logs).

- **IAM Role cho CI/CD**:
  - GitHub Actions sử dụng **OIDC** để assume IAM Role trên AWS.
  - Không sử dụng access key tĩnh trong pipeline.
  - Quyền deploy được giới hạn theo scope cần thiết.

Cách tiếp cận này giúp giảm rủi ro rò rỉ thông tin xác thực.

---

### 6. Bảo mật CI/CD và Container

- Docker image được build và push lên **Amazon ECR**.
- ECS service chỉ pull image từ ECR đã được cấp quyền.
- Pipeline CI/CD kiểm soát:
  - Source code
  - Docker image
  - Quy trình deploy

- Mỗi lần deploy đều thông qua pipeline, hạn chế thao tác thủ công trên môi trường production.

---

### 7. Logging, Monitoring và Audit

- **CloudWatch Logs** ghi nhận log từ các ECS services.
- Health check của ALB giúp phát hiện service lỗi và tự động loại bỏ task không hoạt động.
- Log được sử dụng để:
  - Theo dõi sự cố
  - Phân tích hành vi bất thường
  - Phục vụ audit khi cần thiết

---

### 8. Tổng kết

Kiến trúc bảo mật của hệ thống đảm bảo:

- Phân tách rõ ràng giữa các tầng và các thành phần
- Giảm thiểu bề mặt tấn công từ bên ngoài
- Kiểm soát chặt chẽ quyền truy cập và dữ liệu
- Phù hợp với mô hình cloud-native và microservices

Thiết kế này cho phép hệ thống mở rộng linh hoạt trong khi vẫn đảm bảo mức độ bảo mật cần thiết.