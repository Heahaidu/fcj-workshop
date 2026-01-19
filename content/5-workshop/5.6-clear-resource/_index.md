---
title : "Dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 6
pre: <b> 5.6 </b>
chapter : false
---

**Xin chúc mừng bạn đã hoàn thành xong lab này!** Trong bài lab này, bạn đã học về kiến trúc và cách triển khai CI/CD Pipeline một ứng dụng microservice và static web bằng CloudFormation và Git Action.

## 🎯 Mục tiêu bài lab

* Tạo hạ tầng AWS bằng CloudFormation.
* Khởi chạy backend bằng bootstrap image trên Docker Hub để ECS service đạt trạng thái healthy ngay khi tạo stack.
* Build và push image backend lên ECR bằng GitHub Actions.
* Update CloudFormation để chuyển ECS task từ bootstrap image sang image trên ECR.
* Triển khai frontend và phân phối qua S3 và CloudFront.
* Bật HTTPS tạm thời cho backend bằng CloudFront để frontend HTTPS có thể gọi backend qua HTTPS.
* Thiết lập SPA routing cho frontend bằng CloudFront Function để refresh và deep link hoạt động đúng.

---

## Hướng dẫn dọn dẹp

### Xóa các Stack đã tạo

* Mở **AWS CloudFormation Console**
* Xóa stack đã tạo trong bài thực hành:

  * `internship-project`

> [LƯU Ý]
Nếu bạn không thể xóa stack vì S3 và ECR không thể xóa bạn phải xóa thủ công 4 ECR và S3.   
Sau đó bạn có thể quay lại để xóa nốt phần stack còn lại.

---

✅ Như vậy bạn đã hoàn tất việc **dọn dẹp tài nguyên AWS sau lab**, đảm bảo không còn chi phí phát sinh và giữ môi trường AWS sạch sẽ!