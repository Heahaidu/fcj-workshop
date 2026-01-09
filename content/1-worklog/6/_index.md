---
title : "Worklog tuần 6"
date :  "`r Sys.Date()`" 
weight : 6
pre: <b> 1.6 </b>
chapter : false
---

## 📍 Mục tiêu tuần 6
- Triển khai Grafana trên EC2 và đọc metrics từ CloudWatch.
- Hoàn thiện khung UI trên NextJS và Tailwind.
- Hoàn thiện Discover page và component EventCard.
- Tạo hệ thống filter cho Discover page.
- Hoàn thiện Interest page và Activity page.
- Tạo modal base và popup cho Auth và Account.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Tìm hiểu và triển khai Grafana trên EC2 | 10/11/2025 | 10/11/2025 | [FCJ Workshop 29](https://000029.awsstudygroup.com/vi/) |
| 3 | - Thiết kế sidebar và menu chính<br>- Dựng Sidebar trong NextJS<br>- Thêm active state theo route pathname | 11/11/2025 | 11/11/2025 |  |
| 4 | - Thiết kế header và footer<br>- Dựng header sticky và footer trong NextJS<br>- Chuẩn hóa container và z-index cho layout | 12/11/2025 | 12/11/2025 |  |
| 5 | - Thiết kế Discover page và EventCard<br>- Dựng EventCard và grid list| 13/11/2025 | 13/11/2025 |  |
| 6 | - Bổ sung banner và filter cho Discover page<br>- Tạo FilterButton và FilterGroup để tái sử dụng<br>- Hoàn thành workshop Trực quan hóa chi phí bằng Cost Explorer | 14/11/2025 | 14/11/2025 | [FCJ Workshop 34](https://000034.awsstudygroup.com/vi/) |
| 7 | - Thiết kế Interest page<br>- Thiết kế Activity page theo list timeline | 15/11/2025 | 15/11/2025 |  |
| CN | - Thiết kế component popup (Login, Register, Account) | 16/11/2025 | 16/11/2025 |  |

---

## ✅ Kết quả đạt được tuần 6
- Triển khai Grafana và giám sát CloudWatch.
  - Grafana chạy trên EC2 qua port 3000.
  - Dashboard hiển thị CPUUtilization theo InstanceId.

- Hoàn thiện khung layout UI trên NextJS.
  - Sidebar có active state theo route.
  - Header sticky và footer dùng chung cho các trang.

- Hoàn thiện Discover page.
  - EventCard dùng lại được.
  - Có grid list skeleton loading và empty state.
  - Có banner và filter group tái sử dụng.

- Hoàn thành Interest page và Activity page.
  - Interest dùng chip selection với state selected.
  - Activity dùng list timeline với ActivityItem.

- Hoàn thiện modal và popup cơ bản.
  - Modal base dùng lại được.
  - AuthModal và AccountMenu gắn vào header và chạy trên local.

- Hoàn thành workshop chi phí.
  - Xem chi phí theo service và theo tag bằng Cost Explorer.
  - Có danh sách dịch vụ phát sinh chi phí để rà tài nguyên lab.