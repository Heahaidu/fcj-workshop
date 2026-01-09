---
title : "Worklog tuần 1"
date :  "`r Sys.Date()`" 
weight : 1 
pre: <b> 1.1 </b>
chapter : false
---

## 📍 Mục tiêu tuần 1
- Thiết lập bảo mật cơ bản cho tài khoản AWS.
- Làm quen AWS Management Console và các dịch vụ.
- Triển khai kiến trúc mạng VPC (public và private subnet).
- Thực hành EC2 Windows và Linux.
- Truy cập private và kiểm soát quyền bằng IAM.

---

## 📌 Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 6 | - Bật MFA cho root account<br> - Tạo IAM user group role<br>- Làm quen IAM Billing Support Center | 10/10/2025 | 10/10/2025 | [IAM Docs](https://docs.aws.amazon.com/iam/)<br>[IAM Best Practices](https://aws.amazon.com/iam/resources/best-practices/)<br>[FCJ Workshop 1](https://000001.awsstudygroup.com/)<br>[FCJ Workshop 2](https://000002.awsstudygroup.com/) |
| 7 | - Làm quen với các dịch vụ (VPC, EC2, CloudFormation) <br>- Tập vẽ sơ đồ kiến trúc mạng AWS | 11/10/2025 | 11/10/2025 | [VPC Docs](https://docs.aws.amazon.com/vpc/)<br>[NAT Gateway Docs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)<br>[EIC Endpoint Docs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-with-ec2-instance-connect-endpoint.html)<br>[Session Manager Docs](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)<br>[FCJ Workshop 3](https://000003.awsstudygroup.com/) |
| CN | - Khám phá EC2 Windows và Linux<br>- Thực hành Snapshot và AMI<br>- Khôi phục truy cập khi mất keypair<br>- Deploy ứng dụng demo<br>- Thực hành IAM condition | 12/10/2025 | 12/10/2025 | [EC2 Docs](https://docs.aws.amazon.com/ec2/)<br>[AMI Docs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)<br>[Systems Manager Docs](https://docs.aws.amazon.com/systems-manager/)<br>[IAM Condition Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)<br>[FCJ Workshop 4](https://000004.awsstudygroup.com/) |

---

## ✅ Kết quả đạt được tuần 1

- Thiết lập bảo mật tài khoản AWS:
  - Root account bật MFA.
  - Không sử dụng root cho công việc hằng ngày.
  - Phân quyền bằng IAM user group và role.

- Làm quen AWS Management Console:
  - IAM.
  - EC2.
  - VPC.
  - S3.
  - Billing và Support Center.

- Xây dựng kiến trúc mạng AWS:
  - VPC với CIDR riêng.
  - Public subnet và private subnet theo nhiều AZ.
  - Internet Gateway cho public subnet.
  - NAT Gateway cho private subnet.

- Thực hành truy cập EC2 private:
  - EC2 Instance Connect Endpoint.
  - Systems Manager Session Manager.

- Triển khai EC2:
  - Windows Server (RDP).
  - Linux (SSH).
  - Tạo Snapshot và Custom AMI Windows.
  - Launch instance từ AMI.

- Thực hành khôi phục truy cập:
  - Windows khôi phục password bằng Systems Manager.
  - Linux thay public key bằng user data.

- Deploy ứng dụng demo:
  - Linux với Apache MariaDB PHP phpMyAdmin và NodeJS.
  - Windows với XAMPP và NodeJS.

- Thực hành IAM kiểm soát chi phí và rủi ro:
  - Giới hạn region.
  - Giới hạn instance family và instance type.
  - Bắt buộc EBS gp3.
  - Giới hạn IP và thời gian terminate.