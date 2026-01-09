---
title : "Đánh giá rủi ro"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 6. </b> "
---

## 1. Ma trận rủi ro

| ID | Rủi ro | Mô tả | Khả năng xảy ra | Mức độ ảnh hưởng | Mức rủi ro |
|----|-------|------|----------------|------------------|-----------|
| R1 | Quá tải hệ thống | Lưu lượng tăng nhanh. ECS scale không kịp. Hàng đợi và timeout tăng. | Trung bình | Cao | Cao |
| R2 | Lỗi deploy | Sai biến môi trường, sai cấu hình task, sai image tag. Service không lên hoặc lỗi logic. | Trung bình | Trung bình | Trung bình |
| R3 | Sự cố kết nối CSDL | Hết connection, timeout, DNS lỗi nội bộ. Ảnh hưởng 1 hoặc nhiều CSDL. | Trung bình | Cao | Cao |
| R4 | Tấn công bảo mật (DDoS/abuse) | DDoS L3/L4 hoặc flood L7, brute force, bot abuse | Cao | Cao | Cao |
| R5 | Sự cố bảo mật | DDoS, brute force, lộ secret, lỗi phân quyền, SQL injection. | Thấp | Cao | Trung bình |
| R6 | Lỗi Redis hoặc Kafka trên ECS | Task restart. Mất dữ liệu tạm. Consumer lag tăng. Chậm xử lý sự kiện. | Trung bình | Trung bình | Trung bình |

> [!WARNING]
> R1, R2 và R3 cần ưu tiên xử lý trong thiết kế và triển khai.

---

## 2. Chiến lược giảm thiểu rủi ro

### R1. Quá tải hệ thống
- Thiết lập ECS Auto Scaling theo CPU, RAM, và request count trên ALB.
- Giới hạn số task tối đa để kiểm soát chi phí
- Đặt health check rõ ràng. Tách liveness và readiness.
- Giới hạn tốc độ tại API Gateway. Áp quota theo API key nếu có.
- CloudFront cache frontend nhằm giảm tải cho backend.
- Tối ưu truy vấn. Thêm index theo truy vấn chính.

### R2. Lỗi deploy
- Khóa version bằng image digest hoặc tag theo commit.
- Chạy test tự động trước khi deploy. Tích hợp kiểm thử cho API và DB.
- Dùng rolling update (Blue/Green). Bật rollback khi health check fail.
- Tách môi trường dev và production. Deploy dev trước production.
- Quản lý cấu hình bằng IaC. Review thay đổi qua pull request.

### R3. Sự cố kết nối CSDL
- Giới hạn max connection tại ứng dụng. Bật connection pool.
- Bật statement timeout. Bật idle timeout.
- Tách DB user theo service. Giảm quyền. Giảm rủi ro thao tác sai.
- Giám sát RDS metrics. Theo dõi CPU, FreeableMemory, DatabaseConnections, ReadLatency, WriteLatency.
- Cân nhắc RDS Proxy khi số connection dao động mạnh.

### R4 – Tấn công bảo mật (DDoS/abuse)
- Đặt CloudFront làm entrypoint cho web, gắn AWS WAF vào CloudFront (hoặc ALB).
- Bật AWS WAF:
  - Rate-based rule (giới hạn request/IP).
  - AWS Managed Rules (Common, KnownBadInputs, SQLi, IP reputation).
- Giới hạn tốc độ trên API Gateway (throttling) và trả 4xx khi vượt ngưỡng.
- Bắt buộc xác thực cho API nhạy cảm, hạn chế endpoint public không cần thiết.
- Bật log/metrics để phát hiện sớm (CloudFront/ALB/API Gateway, WAF blocked).

### R5. Sự cố bảo mật
- Không public ECS và RDS. Chỉ public entrypoint cần thiết.
- Dùng IAM theo nguyên tắc tối thiểu quyền. Tách role theo service.
- Lưu secret bằng Secrets Manager hoặc SSM Parameter Store. Rotate khi cần.
- Bật WAF cho CloudFront hoặc ALB. Bật rate-based rule cho endpoint nhạy cảm.
- Bật log audit. Theo dõi 4xx, 5xx, và pattern bất thường.

### R6. Lỗi Redis hoặc Kafka trên ECS
- Đặt resource limit rõ ràng. Tránh OOM kill.
- Bật persistent storage nếu dùng Kafka tự quản. Giữ dữ liệu qua restart.
- Thiết lập consumer group đúng. Theo dõi lag.
- Thiết kế idempotent cho consumer. Tránh xử lý lặp gây sai dữ liệu.
- Đặt TTL cho cache. Chấp nhận cache miss khi Redis restart.

---

## 3. Kế hoạch dự phòng

- Sự cố backend:
  - ECS tự thay task khi health check fail.
  - Tăng task bằng Auto Scaling.
  - Tắt tạm tính năng nặng. Giảm tải lên DB.
  - Khi backend không đáp ứng hiệu năng yêu cầu, tiến hành phân tích bottleneck và tối ưu lại code, sau đó deploy phiên bản đã sửa.

- Sự cố database:
  - Restore snapshot cho từng DB bị ảnh hưởng.
  - Chuyển cấu hình kết nối sang DB mới sau khi restore.
  - Nâng cấp Multi-AZ khi yêu cầu downtime thấp.

- Sự cố DDoS/abuse:
  - Kích hoạt rule khẩn cấp trên WAF (rate-limit thấp hơn).
  - Block theo IP/ASN/geo tạm thời nếu cần.
  - Giảm tải backend bằng cache và tắt tính năng không thiết yếu trong thời điểm bị tấn công.

- Sự cố deploy:
  - Rollback task definition phiên bản trước.
  - Giữ image cũ trong ECR. Đặt retention theo số bản phát hành.

- Sự cố Redis hoặc Kafka:
  - Restart service.
  - Rebalance consumer.
  - Rebuild cache theo chiến lược lazy-load.

- Sự cố frontend:
  - Redeploy build lên S3.
  - Invalidate cache CloudFront theo đường dẫn cần thiết.

---

### Đánh giá tổng quan

Rủi ro tập trung ở tải cao và phụ thuộc CSDL.  
Single AZ giảm chi phí nhưng tăng thời gian gián đoạn khi RDS lỗi.  
ECS Auto Scaling giảm rủi ro quá tải nhưng không thay thế tối ưu DB và giới hạn kết nối.