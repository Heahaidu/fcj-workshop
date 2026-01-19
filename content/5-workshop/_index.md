---
title : "Workshop"
date :  "`r Sys.Date()`" 
weight : 5
pre: <b> 5. </b>
chapter : false
---

## Tổng quan bài lab

Bài lab triển khai một hệ thống web trên AWS bằng CloudFormation.

CloudFormation tạo hạ tầng trước.
- VPC và các thành phần mạng cần thiết
- ECS Fargate service cho backend
- ALB và health check
- ECR để lưu image
- S3 để lưu frontend
- CloudFront để phân phối frontend
- IAM role và policy phục vụ triển khai

Giai đoạn khởi tạo dùng bootstrap image.
- ECS chạy image Spring Boot trên Docker Hub
- Ứng dụng có sẵn endpoint ` /actuator/health/liveness `
- ALB health check gọi endpoint này
- CloudFormation tạo stack thành công và xuất Outputs

Sau khi stack sẵn sàng.
- Lấy Outputs từ CloudFormation
- Khai báo GitHub Actions Variables và Secrets
- Build và push image backend lên ECR
- Update stack để ECS dùng image thật từ ECR

Frontend chạy qua HTTPS trên CloudFront.
Backend phải có HTTPS để frontend gọi được.
Lab dùng CloudFront để cấp HTTPS tạm cho backend.
- Tạo origin trỏ tới API Gateway của stack
- Tạo behavior `/api/*` để route request API qua origin này

Frontend cần SPA routing.
Lab cấu hình CloudFront Function.
- Tạo function `interest-router`
- Gắn function vào viewer request của default behavior

### Nội dung chính
1. [Giới thiệu](5.1-about/)         
2. [Các bước chuẩn bị](5.2-step/)       
3. [HTTPS đối với Backend và SPA cho Frontendd](5.3-be-https/)  
4. [Kiểm tra kết quả](5.4-clear-resource/)  
5. [Video triển khai](5.5-video/)   
6. [Dọn dẹp tài nguyên](5.6-clear-resource/)    