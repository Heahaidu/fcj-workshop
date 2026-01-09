---
title : "Worklog tuần 2"
date :  "`r Sys.Date()`" 
weight : 2 
pre: <b> 1.2 </b>
chapter : false
---

## 📍 Mục tiêu tuần 2
- Triển khai mô hình 2 tầng EC2 và RDS.
- Thực hành Auto Scaling với ALB và ASG.
- Thiết lập Budgets và theo dõi metrics bằng CloudWatch.
- Thực hành Hybrid DNS với Route 53 Resolver.
- Thực hành AWS CLI theo nhóm dịch vụ.
- Thực hành AWS Backup.
- Thực hành VM Import Export.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Thực hành làm quen với RDS <br>- Deploy Node.js và kết nối DB qua RDS endpoint | 13/10/2025 | 13/10/2025 | [VPC Docs](https://docs.aws.amazon.com/vpc/)<br>[RDS Docs](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)<br>[FCJ Workshop 5](https://000005.awsstudygroup.com/vi/) |
| 3 | - Thực hành làm quen với AWS Auto Scaling Group và triển khai ứng dụng | 14/10/2025 | 14/10/2025 | [FCJ Workshop 6](https://000006.awsstudygroup.com/vi)<br>[ALB Docs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)<br>[EC2 Auto Scaling Docs](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)<br>[CloudWatch Custom Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html) |
| 4 | - Thực hành quản lý chi phí với AWS Budget <br>- Thực hành và tìm hiểu AWS Cloudwatch | 15/10/2025 | 15/10/2025 | [AWS Budgets Docs](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html)<br>[CloudWatch Metrics Docs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)<br>[CloudFormation Docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)<br>[FCJ Workshop 7](https://000007.awsstudygroup.com/vi)<br>[FCJ Workshop 8](https://000008.awsstudygroup.com/vi) |
| 5 | - Thực hành và làm quen với Route 53 | 16/10/2025 | 16/10/2025 | [FCJ Workshop 10](https://000010.awsstudygroup.com/vi)<br>[Route 53 Resolver Docs](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver.html)<br>[AWS Directory Service Docs](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html)<br>[Quick Start Template](https://aws-quickstart.s3.amazonaws.com/quickstart-microsoft-rdgateway/templates/rdgw-master.template) |
| 6 | - Thực hành và làm quen với AWS CLI | 17/10/2025 | 17/10/2025 | [FCJ Workshop 11](https://000011.awsstudygroup.com/vi)<br>[AWS CLI Install](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) |
| 7 | - Thực hành triển khai Backup cho hệ thống | 18/10/2025 | 18/10/2025 | [FCJ Workshop 13](https://000013.awsstudygroup.com/vi/)<br>[AWS Backup Docs](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)<br>[Backup Vault Notifications](https://docs.aws.amazon.com/cli/latest/reference/backup/put-backup-vault-notifications.html)<br>[CloudFormation Docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| CN | - Thực hành về VM Import/Export | 19/10/2025 | 19/10/2025 | [FCJ Workshop 14](https://000014.awsstudygroup.com/vi/)<br>[VM Import Export Docs](https://docs.aws.amazon.com/vm-import/latest/userguide/what-is-vmimport.html)<br>[CLI import-image](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/import-image.html)<br>[CLI export-image](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/export-image.html) |

---

## ✅ Kết quả đạt được tuần 2
- Hoàn thành mô hình 2 tầng EC2 và RDS.
  - RDS chạy private subnets.
  - DB chỉ mở 3306 từ SG app.

- Hoàn thành lab Auto Scaling.
  - Tạo AMI và Launch Template.
  - Tạo ALB và Target Group port 5000.
  - Tạo ASG và kiểm thử manual scaling.
  - Tạo scheduled scaling và quan sát activity.
  - Publish custom metrics lên CloudWatch.

- Thiết lập quản lý chi phí và quan sát.
  - Tạo Cost Budget theo tháng.
  - Tạo Usage Budget theo EC2 Running Hours.
  - Thực hành CloudWatch metrics và biểu đồ.

- Hoàn thành lab Hybrid DNS.
  - Deploy Quick Start.
  - Tạo Managed Microsoft AD.
  - Tạo Route 53 Resolver endpoints và resolver rule.
  - Kiểm thử nslookup và Resolve-DnsName.

- Thực hành AWS CLI.
  - Cài AWS CLI v2.
  - Cấu hình profiles.
  - Thao tác S3 SNS IAM VPC EC2.
  - Tạo EC2 bằng CLI và SSH thành công sau khi sửa lỗi.

- Hoàn thành lab AWS Backup.
  - Deploy stack bằng CloudFormation.
  - Chạy backup và restore test.
  - Nhận SNS notifications.
  - Xem CloudWatch logs.

- Hoàn thành lab VM Import Export.
  - Import VM image thành AMI.
  - Launch EC2 từ AMI.
  - Export EC2 và AMI ra S3.