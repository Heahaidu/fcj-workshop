---
title : "Worklog tuần 7"
date :  "`r Sys.Date()`" 
weight : 7
pre: <b> 1.7 </b>
chapter : false
---

## 📍 Mục tiêu tuần 7
- Hoàn thiện Settings popup theo 4 tab và gắn vào account menu.
- Hoàn thành workshop Data Lake trên S3.
- Đổi hướng UI theo reference OpenSea và tạo token layout mới.
- Migrate Discover page theo token mới.
- Tạo Event popup hiển thị nội dung event detail.
- Refactor UI primitives và chuẩn hóa variants.
- Sửa lỗi export code từ Figma bằng workspace mới.
- Dùng công cụ AI để generate UI từ template Figma và tạo prompt mẫu.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Thiết kế settings popup theo 4 tab<br>- Dựng Tabs component dùng chung<br>- Dựng SettingsModal và form section<br>- Gắn settings popup vào account menu | 17/11/2025 | 17/11/2025 |  |
| 3 | - Triển khai workshop Data Lake trên S3 | 18/11/2025 | 18/11/2025 | [FCJ Workshop 35](https://000035.awsstudygroup.com/vi/) |
| 4 | - Tạo layout mới | 19/11/2025 | 19/11/2025 | |
| 5 | - Thiết kế và dựng Event popup<br>- Xác định field event detail cho UI | 20/11/2025 | 20/11/2025 |  |
| 6 | - Refactor UI primitives gồm button input badge modal tabs<br>- Chuẩn hóa props variant size disabled<br>- Gom class trùng vào component base<br>- Sửa EventCard dùng được cho grid và list | 21/11/2025 | 21/11/2025 |  |
| 7 | - Thử export code từ thiết kế trong Figma và ghi lỗi theo nhóm<br>- Tạo workspace mới và copy thiết kế cốt lõi<br>- Chuẩn hóa auto layout constraints naming layer structure<br>- Export theo từng frame và xác nhận chạy trên desktop | 22/11/2025 | 22/11/2025 |  |
| CN | - Dùng Google AI Studio để generate UI từ template Figma <br>- Generate code HTML và ReactJS | 23/11/2025 | 23/11/2025 |  |

---

## ✅ Kết quả đạt được tuần 7
- Hoàn thành Settings popup.
  - Có 4 tab My account My settings Language and Region Notifications.
  - Tabs component dùng lại được.
  - Settings popup mở từ account menu.

- Hoàn thành Data Lake workshop.
  - Tạo cấu trúc dữ liệu theo tầng trên S3.
  - Có checklist cleanup theo bucket và prefix.

- Đổi hướng UI và migrate Discover page.
  - Dùng reference OpenSea để xác định layout mới.
  - Tạo token cho container grid spacing radius shadow border.
  - Discover page và EventCard chạy theo token mới.

- Hoàn thành Event popup.
  - Popup hiển thị cover title host time location description agenda CTA.
  - Có spec field event detail để map API.

- Refactor UI primitives.
  - Chuẩn hóa variant size disabled cho button input badge modal tabs.
  - Giảm class trùng giữa các trang.
  - Dễ mở rộng trang theo token mới.

- Sửa quy trình export từ Figma.
  - Khoanh vùng lỗi theo auto layout constraints naming.
  - Tạo workspace mới làm template.
  - Export chạy được trên desktop.

- Áp dụng AI để generate UI.
  - Generate được code cho một màn hình đại diện.
  - Tạo prompt mẫu để sửa spacing typography grid.
  - Sửa tay phần button variants và hover state.