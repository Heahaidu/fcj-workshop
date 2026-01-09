---
title : "Project proposal"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre: <b> 2. </b>
---

## Event Organization and Community Building Platform  
### Cloud-native trên AWS với Microservices & AI

---

## 1. Mô tả vấn đề

- **Bối cảnh:** Nhu cầu tổ chức sự kiện và xây dựng cộng đồng trực tuyến tăng, lưu lượng truy cập thường **spike** vào các thời điểm cao điểm (mở đăng ký, livestream, giờ diễn ra sự kiện).
- **Vấn đề phía hệ thống:**
  - Khó mở rộng khi traffic tăng đột biến.
  - Chi phí vận hành cao do hạ tầng chưa tối ưu và thiếu tự động hóa.
  - Chưa đáp ứng tốt nhu cầu cá nhân hóa trải nghiệm.
- **Vấn đề phía người dùng:**
  - Thiếu một nền tảng tập trung đủ tiện để tạo/quảng bá/tổ chức sự kiện.
  - Tìm kiếm/lọc sự kiện chưa hiệu quả → mất thời gian tra cứu, giảm trải nghiệm.
  - Thiếu công cụ hỗ trợ thông minh để hỏi–đáp/tra cứu nhanh.
- **Mục tiêu:** Xây dựng nền tảng giao diện thân thiện, tìm kiếm nhanh, có **AI chatbot** hỗ trợ kịp thời, đồng thời đảm bảo **mở rộng, ổn định, bảo mật và tối ưu chi phí**.

---

## 2. Kiến trúc giải pháp

![Solution-Architecture](/2.architect/Solution-Architecture.jpg)

- **Kiến trúc tổng thể:** Cloud-native **microservices** trên AWS, container hóa và chạy trên **ECS Fargate**, triển khai theo mô hình 3-tier mở rộng.
- **Luồng xử lý chính:**
  1) User tải UI qua **CloudFront → S3 WebBucket** (static Next.js).  
  2) Client gọi API qua **API Gateway (HTTP API)**.  
  3) API Gateway dùng **VPC Link** chuyển vào **ALB**.  
  4) **ALB path-based routing** đến từng microservice trên ECS.  
  5) Service đọc/ghi **RDS PostgreSQL**, dùng **Redis** cache, **Kafka** cho async event khi cần.  
  6) Media lưu **S3 ImagesBucket**, log/metrics qua **CloudWatch**.
- **Thành phần chính:**
  - Frontend: Next.js (static) trên **S3 + CloudFront (OAC)**.
  - Backend: 4 services Spring Boot (**User / Event / Notification / AI Chat**), WebSocket cho chatbot.
  - Data: **RDS PostgreSQL** (3 DB instances, notification + chatbot dùng chung 1 DB), private subnet, backup + encryption.
  - Caching/Messaging: **Redis + Kafka** chạy nội bộ trên ECS, **Cloud Map** cho service discovery.
  - Security/Network: VPC 2 AZ, tách **public/private subnet**, **IGW + NAT Gateway**, SG + IAM least privilege.
- **Vận hành:** IaC bằng **CloudFormation**, quan sát bằng **CloudWatch Logs** (retention 14 ngày) và health-check qua ALB.

---

## 3. Triển khai kỹ thuật

- **Giai đoạn triển khai:**
  1) Local development: phát triển độc lập 4 services + FE, Docker/Docker Compose.
  2) Local integration: chạy đồng thời, kiểm thử luồng nghiệp vụ end-to-end.
  3) Manual deploy AWS: build/push image lên **ECR**, tạo ECS Task Definition/Service, cấu hình ALB/RDS/VPC/SG.
  4) IaC + Automation: dựng hạ tầng bằng **CloudFormation** và chuẩn hóa cấu hình.
  5) CI/CD: **GitHub Actions + AWS IAM OIDC**
     - Backend: build → docker build → push ECR → update task definition → rolling update ECS.
     - Frontend: build static → sync S3 → invalidate CloudFront.
- **Kiểm thử & giám sát:** unit/integration/manual/deployment test, theo dõi log/metrics bằng CloudWatch, health-check ALB.
- **Nguyên tắc triển khai an toàn:** tách môi trường (dev/staging/prod) và **deploy staging trước production**, bật rollback theo health-check khi cần.

---

## 4. Lịch trình & các mốc thời gian

- **Timeline theo tuần (tóm tắt):**
  - W1–W2: chốt ý tưởng, pivot từ monolithic → microservices.
  - W3: thiết kế DB theo domain/service, phân rã nghiệp vụ.
  - W4–W6: phát triển frontend (Next.js) + khởi động Event Service, tích hợp FE–BE.
  - W7: Notification Service + Kafka flow (Event publish → Notification consume), refactor luồng gửi email để dễ scale.
  - W8: ổn định hệ thống, fix bug, đồng bộ DTO/luồng.
  - W9: deploy frontend lên AWS (S3 + CloudFront) + CI/CD OIDC, chuẩn bị deploy backend.
  - W10: CloudFormation deploy backend, xử lý lỗi production (DB env/DDL/health-check/HTTPS–HTTP).
  - W11: hoàn thiện worklog, tài liệu và proposal.
- **Mốc nổi bật:**
  - 02/11: chuyển kiến trúc sang microservices.
  - 16/12: frontend live trên AWS + tự động deploy.
  - 22–25/12: CloudFormation deploy + fix ALB unhealthy/SPA routing/HTTPS–HTTP.
  - 03/01: hoàn thiện proposal/tài liệu.

---

## 5. Dự toán ngân sách

- **Giả định:** 1,000,000 request/ngày ~30,000,000/tháng, region **ap-southeast-1 (Singapore)**, 4 microservices + Redis + Kafka trên ECS, 3 RDS Single AZ, FE static qua CloudFront/S3.
- **Ước tính chi phí hạ tầng/tháng (theo 2 kịch bản):**

| Nhóm chi phí | Min | Max | Ghi chú |
|---|---:|---:|---|
| Compute (ECS + ALB) | ~318 | ~526 | 1–2 task/service, đã tính Redis/Kafka |
| API & Network | ~222 | ~345 | API GW + VPC Link + NAT + data transfer |
| Storage & Database | ~240 | ~240 | 3× RDS + storage + S3 + ECR |
| Monitoring | ~10 | ~10 | CloudWatch (tùy log/retention) |
| **Tổng** | **~790 USD/tháng** | **~1120 USD/tháng** | Phụ thuộc task count & data transfer |

- **Cost drivers chính:** Data transfer qua CloudFront, ECS Fargate runtime, RDS, NAT Gateway.
- **Hướng tối ưu:** giảm outbound không cần thiết, cân nhắc VPC Endpoint (S3/ECR), tối ưu cache, giới hạn autoscaling, kiểm soát log retention.

---

## 6. Đánh giá rủi ro

Các rủi ro chính của hệ thống bao gồm: quá tải khi traffic tăng đột biến, lỗi deploy do cấu hình sai, sự cố kết nối cơ sở dữ liệu, tấn công bảo mật và sự cố Redis/Kafka.

Giải pháp giảm thiểu bao gồm:
- ECS Auto Scaling và ALB health check để xử lý tải cao.
- CI/CD chuẩn hóa, rolling update và rollback khi deploy lỗi.
- Giám sát CloudWatch cho ECS, ALB và RDS.
- Phân tách mạng public/private, IAM least privilege và không public backend.
- Sử dụng caching và messaging để giảm tải backend.

Ngoài ra, hệ thống có kế hoạch dự phòng như rollback version, restore snapshot database và tăng tài nguyên tạm thời khi xảy ra sự cố.

---

## 7. Kết quả mong đợi

Hệ thống kỳ vọng đạt được các kết quả sau:

- Thời gian phản hồi API trung bình dưới **300 ms** trong điều kiện tải giả định.
- Khả năng xử lý ổn định khoảng **1.000.000 request/ngày**.
- Uptime mục tiêu **≥ 99%**.
- Khả năng mở rộng tự động mà không cần can thiệp thủ công.
- Quy trình deploy nhanh, ổn định và ít lỗi nhờ CI/CD và IaC.

Về lâu dài, kiến trúc đề xuất tạo nền tảng kỹ thuật vững chắc để mở rộng thành hệ thống production, đồng thời mang lại giá trị học tập cao trong các lĩnh vực **Cloud, DevOps và Microservices**.

---