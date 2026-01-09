---
title : "Worklog tuần 10"
date :  "`r Sys.Date()`" 
weight : 10
pre: <b> 1.10 </b>
chapter : false
---

## 📍 Mục tiêu tuần 10
- Thực hành CloudFormation cơ bản và triển khai stack bằng AWS CLI.
- Thực hành CloudFormation custom resource bằng Lambda để tạo key pair và lưu SSM Parameter.
- Thực hành IAM Role và Condition. Test assume role theo IP và theo thời gian.
- Thực hành cấp quyền truy cập S3 bằng access key và đánh giá rủi ro.
- Thực hành cấp quyền truy cập S3 bằng IAM role gắn vào EC2.
- Triển khai S3 Static Website Hosting và CloudFront.
- Fix bug FE theo backlog và đồng bộ UI theo guideline chung.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Triển khai và nắm AWS CloudFormation cơ bản | 08/12/2025 | 08/12/2025 | [FCJ Workshop 37](https://000037.awsstudygroup.com/vi/) |
| 4 | - Tạo IAM với Condition | 10/12/2025 | 10/12/2025 | [FCJ Workshop 44](https://000044.awsstudygroup.com/vi/) |
| 6 | - Thực hành cấp quyền cho ứng dụng với IAM Role | 12/12/2025 | 12/12/2025 | [FCJ Workshop 48](https://000048.awsstudygroup.com/vi/) |
| CN | - Tìm hiểu và thực hành làm quen với S3 Static web & CloudFront | 14/12/2025 | 14/12/2025 | [FCJ Workshop 57](https://000057.awsstudygroup.com/vi/) |

---

## ✅ Kết quả đạt được tuần 10
- Hoàn thành CloudFormation cơ bản và nâng cao.
  - Template EC2 qua cfn-lint và tạo stack bằng AWS CLI.
  - Custom resource Lambda tạo key pair và lưu private key vào SSM Parameter Store.
  - Fix Lambda response để stack không bị kẹt.

- Hoàn thành workshop IAM Role và Condition.
  - Test quyền theo user group role.
  - Thực hành assume role qua STS.
  - Giới hạn assume role theo IP và theo thời gian.

- Hoàn thành thực hành quyền truy cập S3 cho ứng dụng trên EC2.
  - Upload S3 bằng access key để hiểu rủi ro long term credentials.
  - Upload S3 bằng EC2 instance role và xác nhận credentials tạm thời.
  - AWS CLI chạy được trên EC2 mà không cần aws configure khi có instance profile.

- Hoàn thành S3 Static Website Hosting và CloudFront.
  - Host website trên S3.
  - Phân phối nội dung qua CloudFront với OAI và bucket policy.
  - Bật Versioning và cấu hình Cross Region Replication.

- Tiến hành fix bug FE và đồng bộ UI.
  - Fix nhóm bug do mismatch field và dữ liệu null.
  - Đồng bộ spacing typography và hành vi notification theo guideline chung.