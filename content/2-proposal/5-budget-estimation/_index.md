---
title : "Dự toán ngân sách"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

> [!LƯU Ý]  
> Các chi phí trong phần này chỉ mang tính ước lượng dựa trên giả định kiến trúc và lưu lượng sử dụng. Chi phí thực tế có thể thay đổi theo khu vực AWS và lưu lượng phát sinh.

## 1. Giả định về tải và kiến trúc

Hệ thống hướng đến 1.000.000 request mỗi ngày.  
Tương đương 30.000.000 request mỗi tháng.

Kiến trúc triển khai:
- 4 microservices chạy trên Amazon ECS Fargate
- Frontend tĩnh trên Amazon S3 và Amazon CloudFront
- Application Load Balancer và API Gateway HTTP API
- 3 Amazon RDS for PostgreSQL Single AZ
- Redis và Kafka chạy trên ECS
- Khu vực triển khai: ap-southeast-1 (Singapore)

---

## 2. Chi phí

### a) Compute

#### ECS Fargate

Cấu hình task:
- Mỗi backend service: 1 vCPU, 2GB RAM
- Số task mỗi backend service: 1–2
- Redis: 0.5 vCPU, 1GB RAM, 1 task
- Kafka: 1 vCPU, 2GB RAM, 1 task

Giá Fargate tại Singapore:
- vCPU: **0.05056 USD / vCPU / giờ**
- RAM: **0.00553 USD / GB / giờ**

| Service | vCPU | RAM | Task | Chi phí / tháng |
|---|---:|---:|---:|---:|
| User Service | 1 | 2GB | 1–3 | 44.98 – 134.95 USD |
| Event Service | 1 | 2GB | 1–3 | 44.98 – 134.95 USD |
| Notification Service | 1 | 2GB | 1–3 | 44.98 – 134.95 USD |
| AI Chat Service | 1 | 2GB | 1–3 | 44.98 – 134.95 USD |
| Redis | 0.5 | 1GB | 1 | 22.49 USD |
| Kafka | 1 | 2GB | 1 | 44.98 USD |

Tổng ECS Fargate:
- Min (1 task mỗi backend service): 247.39 USD/tháng
- Max (3 tasks mỗi backend service): 607.26 USD/tháng

---

#### Application Load Balancer

Giá ALB và LCU tại Singapore: 
- ALB: 0.0252 USD/giờ
- LCU: 0.008 USD/giờ 

| Thành phần | Ước tính | Chi phí / tháng |
|---|---|---:|
| ALB | 1 ALB | 18.4 USD |
| LCU | theo tải (1TB - 2TB) | 26.59 - 34.78 USD |

> [!LƯU Ý]
Đây chỉ là chi phí dự tính có thể thay đổi nếu dữ liệu qua ALB biến động

Tổng compute:
- Min: ~273.98 USD mỗi tháng
- Max: ~642.04 USD mỗi tháng

---

### b) API & Network

#### API Gateway (HTTP API)

Giá API Gateway tại Singapore: 1.25 USD/million (First 300 million)

| Thành phần | Ước tính | Chi phí / tháng |
|---|---|---:|
| API Gateway (HTTP API) | 30M request/tháng | 37.5 USD |

#### API Gateway VPC Link

Giá VPC Link tại Singapore:
- Pricing per VPC endpoint per AZ: 0.013 USD/giờ

| Thành phần | Ước tính | Chi phí / tháng |
|---|---:|---:|
| API Gateway VPC Link | 2 AZ × 730h | 18.98 USD |

#### NAT Gateway

Giá NAT Gateway tại Singapore:
- 0.059 USD/giờ mỗi NAT
- 0.059 USD/GB data processed

| Thành phần | Ước tính | Chi phí / tháng |
|---|---:|---:|
| NAT Gateway (giờ chạy) | 2 NAT | 86.14 USD |
| NAT data processed | 1TB → 2TB | 60.42 - 120.83 USD |
| **Tổng NAT** |  | **146.56 - 206.97 USD** |

#### CloudFront 

Giá bậc thang:
- First 1TB: free
- Next 9TB: 0.12 USD/GB

| Thành phần | Ước tính | Chi phí / tháng |
|---|---:|---:|
| CloudFront outbound | 1TB | 0 USD |
| CloudFront outbound | 2TB (vượt 1TB = 1024GB) | 122.88 USD |

Tổng API & Network:
- Min (1TB): 37.50 + 18.98 + 146.56 + 0 = 203.04 USD/tháng
- Max (2TB): 37.50 + 18.98 + 206.97 + 122.88 = 386.33 USD/tháng

---

> [!TIP]  
> Có thể tối ưu bằng cách:
> - Sử dụng VPC Endpoint cho S3/ECR
> - Giảm outbound traffic không cần thiết
> - Cache nội dung qua CloudFront

---

### c) Storage & Database

RDS dùng Single AZ để giảm chi phí.  
Khi cần tăng độ sẵn sàng, hệ thống chuyển sang Multi-AZ.

| Thành phần | Cấu hình | Chi phí / tháng |
|---|---|---:|
| Amazon RDS (PostgreSQL) + gp3 (20GB)| 3 × db.t4g.small, Single AZ | 120 USD |
| Amazon S3 | 50 GB static/files | 2 USD |
| Amazon ECR | 10 GB image storage | 1 USD |

Tổng storage & database: 123 USD mỗi tháng

---

### d) Monitoring & CI/CD

RDS dùng Single AZ để giảm chi phí.  
Khi cần tăng độ sẵn sàng, hệ thống chuyển sang Multi-AZ.

| Thành phần | Chi phí / tháng |
|----------|-----------------|
| Amazon CloudWatch | ~10 USD |
| GitHub Actions | Free tier | ~0 USD |

---

### Tổng chi phí hạ tầng ước tính

| Kịch bản | ECS tasks | CloudFront outbound | Tổng / tháng |
|---|---:|---:|---:|
| Min | 1 task mỗi backend service | ~1 TB | ~610 USD |
| Max | 3 tasks mỗi backend service | ~2 TB | ~1161 USD |

---

## Link định giá chính thức của AWS

| STT | AWS Service | Mục đích trong kiến trúc | Link chi phí chính thức |
|---:|---|---|---|
| 1 | Amazon VPC và NAT Gateway | VPC, Subnet, Route Table, Internet Gateway, NAT Gateway | https://aws.amazon.com/vpc/pricing/ |
| 2 | Elastic IP | Gán cho NAT Gateway | https://aws.amazon.com/ec2/pricing/on-demand/ |
| 3 | Amazon S3 (WebBucket, ImagesBucket) | Lưu frontend web và ảnh | https://aws.amazon.com/s3/pricing/ |
| 4 | AWS IAM | Role, Policy, GitHub OIDC | https://aws.amazon.com/iam/pricing/ |
| 5 | Amazon ECR | Lưu container images | https://aws.amazon.com/ecr/pricing/ |
| 6 | Amazon ECS | Orchestrate container services | https://aws.amazon.com/ecs/pricing/ |
| 7 | AWS Fargate | Chạy container | https://aws.amazon.com/fargate/pricing/ |
| 8 | Application Load Balancer (ALB) | Load balancing và path routing | https://aws.amazon.com/elasticloadbalancing/pricing/ |
| 9 | Amazon API Gateway (HTTP API) và API Gateway VPC Link | Public API entrypoint, kết nối API Gateway tới ALB | https://aws.amazon.com/api-gateway/pricing/ |
| 10 | Amazon RDS (PostgreSQL) | Database cho microservices | https://aws.amazon.com/rds/postgresql/pricing/ |
| 11 | Amazon CloudWatch Logs | Lưu log ECS và ALB | https://aws.amazon.com/cloudwatch/pricing/ |
| 12 | AWS Cloud Map | Service Discovery | https://aws.amazon.com/cloud-map/pricing/ |
| 13 | Amazon CloudFront | CDN phân phối frontend | https://aws.amazon.com/cloudfront/pricing/ |
| 14 | Application Auto Scaling | Auto scale ECS services | https://aws.amazon.com/application-autoscaling/pricing/ |
| 15 | AWS Pricing Calculator | Ước tính tổng chi phí | https://calculator.aws/ |

---

> [!NOTE]
> ### Networking (VPC, Subnet, Route, IGW) & NAT Gateway
> - VPC, Subnet, Route Table, Internet Gateway: **miễn phí**
> - Chỉ tính phí: **NAT Gateway, VPC Endpoint, Data Transfer**
>
> ### Amazon ECS & AWS Fargate
> - Amazon ECS: **Control plane miễn phí**
> - AWS Fargate: **CPU + Memory + Storage**
> - Áp dụng cho:
>   - 4 Service
>   - Redis Service
>   - Kafka Service
>
> ### Application Load Balancer (ALB)
> - Tính theo:
>   - Giờ chạy Load Balancer
>   - LCU (Load Capacity Unit)
>
> ### Amazon RDS for PostgreSQL
> - Tính theo:
>   - DB instance
>   - Storage
>   - Backup
>   - Multi-AZ (nếu bật)
>
> ### AWS Pricing Calculator
> - Dùng để ước tính tổng chi phí hàng tháng cho:
>   - ECS Fargate
>   - RDS
>   - ALB
>   - NAT Gateway
>   - API Gateway
>   - S3 & CloudFront

---

> [!IMPORTANT]
> **Lưu ý quan trọng về chi phí AWS**
> - Các dịch vụ tiêu tốn chi phí lớn nhất:
>   1. NAT Gateway
>   2. ECS Fargate
>   3. Amazon RDS
>   4. ALB và Data Transfer
> - Giá dịch vụ **phụ thuộc region**

---

## 3. Tối ưu chi phí hạ tầng

### 3.1. Tối ưu cấu hình của Fargate cho từng service (task tối đa,...)
- Không nhất thiết service nào cũng cần Max=3:
  - Event: có thể giữ Max=3
  - Notification: thường nhẹ, Max=1–2
  - Chatbot: tuỳ mức dùng, Max=1–2 nếu traffic thấp
- Điều chỉnh target CPU để giảm scale-out.
- Giảm log verbose để giảm CloudWatch Logs.

### 3.2. Tối ưu cấu hình RDS
- Duy trì Single AZ ở giai đoạn đồ án.
- Theo dõi CPU/RAM/connection để chọn size phù hợp (t4g.small là mức ổn để bắt đầu).
- Tối ưu index/query để giảm nhu cầu scale-up.

### 3.3. Sử dụng Cloudflare + S3
- Dùng Cloudflare cache trước S3 (web + images) để giảm băng thông và tăng tốc tải.
- Hạn chế truy cập trực tiếp S3 public cho images; ưu tiên cache/CDN.

---

## 4. Chi phí phát triển (Development Cost)

> Do dự án được thực hiện bởi sinh viên, chi phí phát triển chủ yếu là thời gian và công sức học tập. 
> Việc sử dụng công cụ mã nguồn mở và cloud free-tier giúp giảm đáng kể chi phí phát triển ban đầu.

| Hạng mục | Mô tả |
|--------|------|
| Nhân sự | 3 sinh viên |
| Thời gian phát triển | 2–3 tháng |
| Công cụ | Open-source, miễn phí |
| Chi phí trực tiếp | Gần như 0 USD |

---

## 5.3. Chi phí vận hành (Operational Cost)

> [!TIP]  
> - CI/CD và Auto Scaling giúp giảm đáng kể chi phí vận hành thủ công.
> - Tự động hóa deploy bằng CloudFormation giúp giảm lỗi cấu hình và chi phí vận hành lâu dài.

Hệ thống vận hành qua:
- CI/CD
- ECS Auto Scaling
- CloudWatch monitoring
- Không cần đội ngũ DevOps chuyên biệt.
- Chi phí vận hành đã được gộp trong chi phí hạ tầng.

👉 **Chi phí vận hành thấp**, phù hợp cho hệ thống quy mô nhỏ và trung bình.

---

## 5. Phân tích lợi tức đầu tư (ROI)

> [!TIP]  
> ROI của dự án không chỉ nằm ở giá trị tài chính mà còn ở giá trị học tập và khả năng mở rộng trong tương lai.

### Lợi ích đạt được

Giá trị chính:
- Hệ thống có khả năng xử lý:
  - ~30 triệu request / tháng
  - Tính sẵn sàng cao, dễ mở rộng
- Giá trị học tập:
  - Thực hành AWS, Microservices, CI/CD
  - Triển khai Cloud-native thực tế
- Khả năng mở rộng:
  - Có thể tối ưu chi phí bằng Reserved Instance hoặc Savings Plan

### Đánh giá ROI

| Tiêu chí | Mức độ | Nhận xét |
|--------|-------|---------|
| Chi phí | Trung bình | Chi phí hạ tầng tăng theo ECS và RDS |
| Hiệu năng | Tốt | Đáp ứng 1.000.000 request mỗi ngày |
| Khả năng mở rộng | Tốt | Mở rộng ngang qua ECS Auto Scaling |
| Giá trị học thuật | Rất tốt | Thực hành AWS và kiến trúc microservices |

---

### Kết luận

> [!WARNING]  
> Đây là mô hình chi phí phù hợp cho **đồ án thực tập**, chưa tối ưu hoàn toàn cho hệ thống thương mại quy mô lớn.


Chi phí tập trung vào ba thành phần chính:
- ECS Fargate cho xử lý backend
- Amazon RDS cho lưu trữ dữ liệu
- Network cho truy cập Internet và API

CloudFront chỉ phân phối frontend và tài nguyên tĩnh.  
API traffic không đi qua CloudFront.  
Lưu lượng outbound ở mức vài trăm GB mỗi tháng là hợp lý.

Cấu hình Single AZ được chọn để giảm chi phí.  
Rủi ro gián đoạn tồn tại nhưng chấp nhận được trong giai đoạn phát triển.  
Multi-AZ cần được áp dụng khi số lượng người dùng tăng.

Hệ thống có khả năng mở rộng ngang thông qua ECS Auto Scaling.  
Chi phí tăng tuyến tính theo số task và lưu lượng.

Dự toán phù hợp cho:
- Đồ án
- Thực tập
- Giai đoạn triển khai ban đầu

Khi chuyển sang vận hành dài hạn, hệ thống cần:
- Tối ưu số lượng task
- Giảm chi phí NAT Gateway
- Nâng cấp kiến trúc database theo tải thực tế