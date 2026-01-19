---
title : "Event 1"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.1 </b> "
---

# BÀI THU HOẠCH "Secure Your Applications: AWS Perimeter Protection Workshop"

## Mục Đích Của Sự Kiện

* Chia sẻ best practices trong thiết kế hệ thống (CDN, WAF, CloudFormation, CloudWatch, CloudFront,...)
* Giới thiệu phương pháp bảo mật (AWS WAF, Rate-based rules, Anti-DDOS AMR,...)
* Giới thiệu một số tính năng nổi bật (ALB & CloudFront private)
* Hướng dẫn thiết kế IaC

---

## Danh Sách Diễn Giả

* **Nguyễn Gia Hưng** – Head of Solutions Architect - Viet Nam & Cambodia
* **Julian Ju** – Senior Edge Services Specialist Solutions Architect
* **Kevin Lim** – Senior Edge Services Specialist GTM

---

## Nội Dung Nổi Bật

### 1. Mô hình định giá mới
- Tập trung vào tính dự đoán chi phí
- Tránh tình trạng hóa đơn tăng bất ngờ

### 2. Tính năng hạ tầng mới
- VPC Origin (ALB/CloudFront trong private subnet)

### 3. AWS WAF và mối đe dọa
- DDoS tăng
- AI bot tăng
- Nhu cầu lọc request ở biên và phân loại bot ngày càng quan trọng

---

## Những Gì Học Được

### Tư duy thiết kế bảo mật
- Chặn sớm ở biên để giảm tải cho origin
- Tách mục tiêu thành 3 nhóm
  - Tính sẵn sàng: chống DDoS, rate limit, giảm tải
  - Tính toàn vẹn: rule chống khai thác lỗ hổng phổ biến
  - Tính kiểm soát truy cập: allowlist, reputation, mTLS

### Tư duy triển khai rule theo hiệu suất
- Rule rẻ đặt trước, rule tốn tài nguyên đặt sau
- Cho phép và chặn theo IP đặt đầu để cắt sớm
- Dành cơ chế ngoại lệ để giảm false positive
- Đo và điều chỉnh bằng log và dashboard

### Tư duy kiến trúc: giảm bề mặt tấn công của origin
- VPC Origin giúp origin không cần public exposure
- CloudFront trở thành điểm vào chính
- Giảm rủi ro quét cổng và tấn công trực tiếp vào ALB

### Tư duy quan sát hệ thống
- Không chỉ chặn, còn phải quan sát
- Kết hợp đo trải nghiệm người dùng và đo hạ tầng
- Dùng dữ liệu quan sát để tinh chỉnh cache, rule, và năng lực chịu tải

---

## Ứng Dụng Vào Công Việc

### Chuẩn hóa kiến trúc phân phối nội dung và API
- Đặt CloudFront trước FE và API khi cần HTTPS, caching, và WAF
- Tách behavior theo path
  - `/api/*` đi về origin API
  - asset tĩnh đi về S3 origin
- Thiết lập cache key rõ ràng theo query, header, cookie cần thiết

### Lập kế hoạch WAF rules theo thứ tự ưu tiên
- Bắt đầu từ allowlist và blocklist
- Thêm rate limit cho endpoint nhạy cảm
  - đăng nhập
  - quên mật khẩu
  - tạo đơn
- Bật reputation và anonymous IP cho các hệ thống public
- Tạo ngoại lệ cho các API hợp lệ hay bị match nhầm

### Đối phó bot thực dụng
- Phân loại bot theo mục tiêu
  - scraping
  - credential stuffing
  - tạo tài khoản hàng loạt
- Dùng Bot Control hoặc rule dựa trên hành vi
- Bổ sung challenge khi cần
- Theo dõi tỷ lệ false positive để tránh chặn nhầm khách thật

### Tối ưu chi phí qua caching và nén
- Bật nén tự động cho nội dung phù hợp
- Dùng short TTL cho nội dung động có thể cache
- Bật error caching hợp lý để giảm áp lực khi origin lỗi
- Giảm tải CPU origin bằng cách đẩy việc nhẹ ra biên
  - redirect
  - set cookie
  - CORS đơn giản qua CloudFront Functions

---

## Trải Nghiệm Trong Event

* Học hỏi từ các chuyên gia về các thiết kế bảo mật

## Một số hình ảnh buổi workshop

![1](/4/1.jpg)
![2](/4/2.jpg)
![3](/4/3.jpg)
![4](/4/4.jpg)