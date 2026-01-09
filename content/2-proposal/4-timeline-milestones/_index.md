---
title : "Lịch trình & Các mốc thời gian"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

Phần này tổng hợp tiến độ dự án theo **mốc thời gian thực tế** (worklog) nhằm đảm bảo tính minh bạch về quá trình phát triển, các mốc hoàn thành quan trọng, quan hệ phụ thuộc và phân bổ nguồn lực.

---

## 1. Project Timeline (theo tuần)

| Tuần | Thời gian | Trọng tâm | Công việc chính (tóm tắt) | Đầu ra / Kết quả | Phụ trách |
|---|---|---|---|---|---|
| W1 | 20/10 – 26/10 | Ideation | 20/10 họp nhóm nêu ý tưởng sản phẩm. 25/10 họp chốt ý tưởng theo hướng **monolithic** (thiết kế 1 website). | Ý tưởng + scope ban đầu | Nhóm |
| W2 | 27/10 – 02/11 | Pivot kiến trúc | 02/11 họp nhóm chỉnh sửa kiến trúc từ monolithic → **microservices** (dựa trên định hướng đã thống nhất). | Kiến trúc tổng thể microservices (bản cập nhật) | Nhóm |
| W3 | 03/11 – 09/11 | Thiết kế DB (microservices) | 04–08/11 thiết kế lại database theo microservices. 09/11 họp xác nhận DB, giải thích mục đích bảng/kiểu dữ liệu/kiến trúc và chia việc. | DB design v2 + phân rã domain theo service | Nhóm (kiến trúc & phân rã service) |
| W4 | 10/11 – 16/11 | Frontend (phase 1) | 11–16/11 thiết kế & xây dựng FE bằng Next.js theo hướng Twitter/Facebook/LinkedIn/Luma. | FE prototype bản đầu | H.Dương |
| W5 | 17/11 – 23/11 | Frontend (phase 2) | 19/11 đánh giá UI chưa phù hợp → đổi hướng, tìm ý tưởng mới, tham khảo OpenSea. | FE direction mới (UI/UX) | H.Dương |
| W6 | 24/11 – 30/11 | FE demo + khởi động Event Service | 26/11 FE hoàn tất ~70% và demo team. Bắt đầu Event Service: học Spring Boot/microservices/giao tiếp/bảo mật/service discovery (Eureka → định hướng Cloud Map). 27–29/11 thiết kế & phát triển API Event (list/detail/filter/interest/registration…). 30/11 bắt đầu phase tích hợp FE–BE. | FE demo ổn, Event Service API khung, bắt đầu tích hợp | H.Dương |
| W7 | 01/12 – 07/12 | Notification Service + Kafka + tích hợp | 01/12 thiết kế DB Notification. 02/12 dựng Notification Service (entity/controller/API). 03/12 tích hợp Kafka: Event publish → Notification consume (test Postman). 04/12 refactor luồng gửi email: chuyển gửi email sang Notification Service để tránh overload Event Service và hỗ trợ scale theo queue. 05–06/12 chỉnh FE + Notification giao tiếp. 07/12 bắt đầu phase fix UI/bug tổng. | Notification Service chạy ổn + Kafka flow, integration cơ bản FE–BE | H.Dương |
| W8 | 08/12 – 14/12 | Stabilize & polish | 07–15/12 tập trung fix bug FE và chỉnh UI theo góp ý thành viên. Hoàn thiện đồng bộ dữ liệu/DTO giữa FE và BE (create/edit event, loại bỏ field dư). | FE/BE khớp dữ liệu hơn, UI ổn định hơn | Nhóm |
| W9 | 15/12 – 21/12 | Deploy Frontend + chuẩn bị Deploy Backend | 16/12 deploy FE lên AWS S3 + CloudFront (tạo S3/CloudFront/IAM Role bằng CloudFormation, dùng GitHub Actions auto deploy). 17/12 thêm S3 cho Event Service để upload ảnh khi tạo event. 18–19/12 thiết kế kiến trúc để đưa BE lên môi trường thực tế (setup thủ công để debug, có lúc phải mở public subnet để bắt lỗi). 20/12 setup môi trường thành công & bắt đầu viết CloudFormation cho backend. 21/12 tiếp tục CloudFormation. | FE live trên AWS, BE chuẩn bị hạ tầng, CloudFormation draft | H.Dương |
| W10 | 22/12 – 28/12 | CloudFormation deploy + fix production issues | 22/12 triển khai CloudFormation, push image lên ECR test deploy, gặp lỗi ALB unhealthy/ECS recreate task. 22–24/12 fix lần lượt: env vars DB, kết nối DB, cơ chế tạo table (ddl-auto), health check endpoint (Actuator). 25/12 xử lý vấn đề FE gọi BE (HTTPS/HTTP) và routing SPA: tạm thời thêm API Gateway và CloudFront Function để xử lý điều hướng. 26/12 kiểm thử chức năng cơ bản và phân công sửa. 27/12 viết proposal báo cáo. | Hạ tầng chạy được, ALB healthy, FE gọi được BE (tạm thời theo giải pháp đã áp dụng), proposal | Nhóm |
| W11 | 29/12 – 04/01 | Hoàn thiện tài liệu | 28/12 – 03/01 tổng hợp và hoàn thiện worklog, chỉnh sửa tài liệu và chuẩn bị phần báo cáo cuối. | Worklog/tài liệu hoàn thiện | H.Dương |

**Ghi chú về User Service & AI Chat Service:**  
Hai service này do thành viên khác phát triển nên không có worklog chi tiết theo ngày trong tài liệu này. Tuy nhiên, chúng được phát triển **song song** trong giai đoạn hệ thống đã chuyển sang microservices (ước lượng từ **W3–W8**) để kịp tích hợp và triển khai chung.

---

## 2. Các mốc quan trọng

- **20/10/2025 (W1):** Kick-off, nêu ý tưởng sản phẩm.
- **25/10/2025 (W1):** Chốt ý tưởng sản phẩm (bản monolithic).
- **02/11/2025 (W2):** Pivot kiến trúc sang microservices.
- **08/11/2025 (W3):** Hoàn tất thiết kế DB theo microservices.
- **09/11/2025 (W3):** Meeting xác nhận DB + giải thích thiết kế + chia việc, đề xuất 4 services (user+page, notification, event, ai).
- **26/11/2025 (W6):** FE đạt ~70% và demo, khởi động Event Service.
- **04/12/2025 (W7):** Refactor luồng gửi email: chuyển sang Notification Service đọc Kafka để tránh overload và hỗ trợ scaling.
- **16/12/2025 (W9):** Frontend deploy lên AWS (S3 + CloudFront) và tự động hóa deploy bằng GitHub Actions (OIDC).
- **24/12/2025 (W10):** Fix ALB unhealthy nhờ bổ sung health endpoint cấu hình phù hợp.
- **25/12/2025 (W10):** Giải quyết vấn đề FE–BE (HTTPS/HTTP) và SPA routing bằng phương án tích hợp API Gateway/CloudFront Function (tạm thời).
- **27/12/2025 (W10):** Hoàn thiện worklog/tài liệu.
- **03/01/2026 (W11):** Hoàn thiện proposal báo cáo.

---

## 4.3. Mối quan hệ phụ thuộc giữa các công việc và thành phần trong dự án.

- **Ý tưởng & scope** (W1) → là đầu vào để **thiết kế kiến trúc** (W2).
- **Kiến trúc microservices** (W2) → quyết định cách **phân rã database** (W3) và phạm vi từng service.
- **DB design theo service** (W3) → là nền tảng để triển khai API/Entity (W6–W7) và tích hợp FE–BE (W6–W8).
- **API & model ổn định** (W6–W8) → là điều kiện cần để deploy hạ tầng backend và scale ổn định trên AWS (W9–W10).
- **CloudFormation deploy** (W10) phụ thuộc mạnh vào:
  - Cấu hình **environment variables** (DB endpoints, credentials, bucket…)
  - **Health check endpoint** đúng chuẩn ALB
  - Cơ chế **khởi tạo schema** (DDL migration) để service kết nối DB thành công
- **FE live trên CloudFront** (W9) → cần endpoint backend phù hợp (HTTPS), vì vậy phát sinh bước xử lý HTTPS/HTTP (W10).

---

## 4.4. Phân bổ nguồn lực

### Phân bổ theo vai trò (tổng quan)
- **H.Dương (vai trò chính):**
  - Thiết kế kiến trúc cho team (microservices + hướng triển khai AWS)
  - Phát triển **Frontend (Next.js)** + UI/UX
  - Phát triển **Event Service** và **Notification Service**
  - Tích hợp Kafka (Event → Notification), tối ưu luồng email để hỗ trợ scaling
  - Triển khai FE lên AWS, tham gia thiết kế/triển khai CloudFormation và debug hệ thống

- **Các thành viên khác (tổng quan):**
  - Phát triển **User(+Page) Service**
  - Phát triển **AI Chat Service**
  - Tham gia test, review UI/UX, tích hợp và hoàn thiện tài liệu/demo

### Phân bổ theo giai đoạn (ước lượng effort)
- **W1–W3 (20%):** Phân tích, kiến trúc, thiết kế DB, phân công.
- **W4–W8 (55%):** Implement FE + BE (Event/Notification), tích hợp, fix bug.
- **W9–W10 (20%):** Deploy AWS, CloudFormation, CI/CD, debug production issues.
- **W11 (5%):** Hoàn thiện worklog/tài liệu/báo cáo.