---
title : "Các giai đoạn triển khai"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

Quá trình triển khai hệ thống được chia thành các giai đoạn rõ ràng nhằm đảm bảo tính đồng bộ và giảm rủi ro trong quá trình phát triển:

### Giai đoạn 1: Phát triển cục bộ

Hệ thống gồm bốn microservices:

  - User Service
  - AI Chat Service
  - Notification Service
  - Event Service

Mỗi service được phát triển và chạy độc lập trên môi trường local.  
Mỗi service có Dockerfile và cấu hình riêng.

Các service được kiểm tra logic nghiệp vụ và API độc lập.  
Frontend kết nối thử nghiệm với từng service trong môi trường local.

Cấu trúc microservice có 4 service tương ứng với 4 docker image

<!-- - Các file GitHub Actions workflow cho 4 service:
  - [file cấu hình chatbot-service](https://github.com/Heahaidu/interest-project/blob/main/.github/workflows/deploy-chatbot-service.yml)
  - [file cấu hình user-service](https://github.com/Heahaidu/interest-project/blob/main/.github/workflows/deploy-user-service.yml)
  - [file cấu hình event-service](https://github.com/Heahaidu/interest-project/blob/main/.github/workflows/deploy-event-service.yml)
  - [file cấu hình notification-service](https://github.com/Heahaidu/interest-project/blob/main/.github/workflows/deploy-notification-service.yml) -->

<!-- > [!INFO]
> Mục đích của file GitHub Actions workflow là **tự động build**, _push_ **Docker image** và _deploy_ các **service** lên `AWS ECS` mỗi khi push code

- File GitHub Actions workflow cầu hình frontend: [file cấu hình web (front-end)](https://github.com/Heahaidu/interest-project/blob/main/.github/workflows/deploy-web-app.yml)

> [!INFO]
> Mục đích của file GitHub Actions workflow là **tự động build**, _push_ **Docker image** và _deploy_ **front-end lên static** lên `AWS S3` +  `CloudFront` mỗi khi push code -->


### Giai đoạn 2: Tích hợp hệ thống

Toàn bộ backend services được chạy đồng thời.  
Frontend kết nối với các API backend dùng chung.

Luồng nghiệp vụ chính được kiểm tra ở mức hệ thống.  
Giai đoạn này tập trung phát hiện lỗi tích hợp và phụ thuộc giữa các service.

### Giai đoạn 3: Triển khai thủ công lên AWS

Các service backend được container hóa bằng Docker.  
Docker image được push lên Amazon ECR.

#### Build & push Docker image lên ECR

**Bước 1:** AWS CLI lấy authorization token tạm thời từ ECR
```bash
aws ecr get-login-password --region {Region} | docker login --username AWS --password-stdin {AccountID}.dkr.ecr.{Region}.amazonaws.com  
```

> [!GHI CHÚ]    
> Thay đổi _{Region}_ và _{AccountID}_ lần lượt thành vùng mà bạn tạo ECR và ID tài khoản AWS đang triển khai

**Bước 2:** Đóng gói service và đẩy ECR

Đoạn script dưới đây sẽ tự dụng build, gán tag và đẩy image lên ECR tương ứng.

``` bash
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
service_name=$(basename "$(pwd)")

docker build -t $service_name .
docker tag $service_name $AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/$service_name
docker push $AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/$service_name
```

> [!LƯU Ý]  
> Trên máy triển khai phải có AWS CLI và đã đăng nhập tài khoản có ít nhất đủ quyền thao tác với ECR.     
> Phải tạo trước các ECR có cùng tên với service trước khi đóng gói và đẩy lên.

### Giai đoạn 4: Tự động hóa triển khai hạ tầng

Toàn bộ hạ tầng được mô tả bằng AWS CloudFormation.     
Quy trình triển khai được chuẩn hóa và tự động hóa.

CloudFormation chịu trách nhiệm tạo và cấu hình:

- Network và security  
- ECS cluster và services  
- RDS databases  
- API Gateway và ALB  
- CloudFront và S3  

Giai đoạn này loại bỏ cấu hình thủ công và giảm rủi ro triển khai.
  
[Template CloudFormation](https://github.com/Heahaidu/interest-project/blob/main/infra.json)

---

🎬 **CloudFormation Demo Video**  
▶️ [Watch](video/03-Technical-Implementation/CloudFormation.mp4)

---

#### Các bước triển khai CloudFormation trên AWS
**Bước 1:** Chọn Create Stack 
> Vào CloudFormation và Chọn Stack → Chọn Create Stack

![Create Stack](/3.technical-implementation/cloud_formation/create_stack.png)

**Bước 2:** Chọn file cấu hình CloudFormation [có sẵn](https://github.com/Heahaidu/interest-project/blob/main/infra.json)
> Chọn "Choose an existing template" và "Upload a template file". Rồi ấn chọn "Choose file" → Next

![Import File](/3.technical-implementation/cloud_formation/import_file.png)

**Bước 3:** Chờ CloudFormation tạo hoàn tất triển khai hạ tầng
> Chờ tới khi tất cả các Stack được thông báo hoàn tất

![wait](/3.technical-implementation/cloud_formation/wait.png)

Kết quả sau khi triển khai thành công

![result](/3.technical-implementation/cloud_formation/result.png)

---

### Tổng quan

![Solution Architecture](/2.architect/Solution-Architecture.jpg)


#### Các tham số cấu hình CloudFormation

![CloudFormation Stack](/3.technical-implementation/cloud_formation/cloudformation_stack.png)

Đây là các tham số người dùng có thể thay đổi khi tạo CloudFormation stack.  
Các tham số cho phép tùy biến tên tài nguyên, hạ tầng mạng, database, container image và khả năng mở rộng của hệ thống.

---

##### 1. Tham số chung của dự án

- **ProjectName**  
  Tên dự án.  
  Dùng để đặt tên cho các tài nguyên AWS như VPC, ECS Cluster, ECS Service, RDS và CloudFront.

---

##### 2. Tham số mạng

Các tham số xác định cấu trúc mạng VPC và subnet.

- **VpcCidr**  
  Dải địa chỉ CIDR của VPC.

- **PublicSubnet1Cidr**, **PublicSubnet2Cidr**  
  CIDR của hai public subnet ở hai Availability Zone.  
  Dùng cho Application Load Balancer và NAT Gateway.

- **PrivateSubnet1Cidr**, **PrivateSubnet2Cidr**  
  CIDR của hai private subnet ở hai Availability Zone.  
  Dùng cho ECS Fargate, RDS, Redis và Kafka.

---

##### 3. Tham số S3 Bucket

- **ImagesBucketName**  
  Tên bucket lưu trữ dữ liệu media như hình ảnh và file upload.  
  Bucket được gắn thêm AWS Account ID để tránh trùng tên toàn cục.

- **WebBucketName**  
  Tên bucket lưu trữ frontend static website.  
  Bucket được sử dụng làm origin cho CloudFront.

---

##### 4. Tham số tích hợp GitHub CI/CD

Các tham số xác định repository GitHub được phép deploy lên AWS.

- **GitHubOrg**  
  Tên tổ chức hoặc user GitHub.

- **GitHubRepo**  
  Tên repository chứa source code.

- **GitHubBranch**  
  Branch được phép assume IAM Role thông qua GitHub OIDC.  
  Thường sử dụng branch `main`.

---

##### 5. Tham số Database

Các tham số cấu hình cho các RDS PostgreSQL instance.

- **DbUsername**  
  Tên người dùng database.

- **DbPassword**  
  Mật khẩu database.  
  Tham số được ẩn khi nhập.

- **DbPort**  
  Cổng kết nối PostgreSQL.  
  Giá trị mặc định là 5432.

- **DbInstanceClass**  
  Loại instance của RDS.

- **DbAllocatedStorage**  
  Dung lượng lưu trữ cho mỗi database.

- **DbMultiAZ**  
  Bật hoặc tắt chế độ Multi-AZ cho RDS.

---

##### 6. Tham số container và service

- **ContainerPort**  
  Cổng mà container backend lắng nghe.

- **ServiceAName**, **ServiceBName**, **ServiceCName**, **ServiceDName**  
  Tên logic của các microservices:
  - User  
  - Notification  
  - Chatbot  
  - Event  

---

##### 7. Tham số Docker Image và ECR

- **EcrRegion**  
  AWS Region chứa Amazon ECR repository.

- **ImageTag**  
  Tag của Docker image được deploy.

---

##### 8. Tham số định tuyến API

- **ServiceAPaths**  
  Các path API được route đến User Service.

- **ServiceBPaths**  
  Các path API được route đến Notification Service.

- **ServiceCPaths**  
  Các path API được route đến Chatbot Service.

- **ServiceDPaths**  
  Các path API được route đến Event Service.

---

##### 9. Tham số health check

- **HealthCheckPath**  
  Endpoint dùng để kiểm tra trạng thái container.

---

##### 10. Tham số số lượng service

- **DesiredCountA**, **DesiredCountB**, **DesiredCountC**, **DesiredCountD**  
  Số lượng ECS task ban đầu cho từng microservice.

---

##### 11. Tham số Auto Scaling

- **AutoScaleMin**  
  Số lượng task tối thiểu.

- **AutoScaleMax**  
  Số lượng task tối đa.

- **AutoScaleCpuTarget**  
  Ngưỡng CPU trung bình để kích hoạt auto scaling.

---

#### Outputs (Kết quả sau khi deploy)

Phần Outputs cung cấp các thông tin đầu ra quan trọng sau khi CloudFormation stack được triển khai thành công.  
Các giá trị này được dùng để truy cập hệ thống và cấu hình ứng dụng.

```json
 "Outputs": {
    "AlbDnsName": { "Value": { "Fn::GetAtt": ["Alb", "DNSName"] } },
    "HttpApiEndpoint": { "Value": { "Fn::GetAtt": ["HttpApi", "ApiEndpoint"] } },

    "Db1Endpoint": { "Value": { "Fn::GetAtt": ["Db1", "Endpoint.Address"] } },
    "Db2Endpoint": { "Value": { "Fn::GetAtt": ["Db2", "Endpoint.Address"] } },
    "Db3Endpoint": { "Value": { "Fn::GetAtt": ["Db3", "Endpoint.Address"] } },

    "ImagesBucket": { "Value": { "Ref": "ImagesBucket" } },
    "WebBucket": { "Value": { "Ref": "WebBucket" } },

	"CloudFrontDomain": { "Value": { "Fn::GetAtt": ["WebDistribution", "DomainName"] } },
	"CloudFrontDistributionId": { "Value": { "Ref": "WebDistribution" } },

    "EcsTaskRoleArn": { "Value": { "Fn::GetAtt": ["EcsTaskRole", "Arn"] } },
    "GitHubDeployRoleArn": { "Value": { "Fn::GetAtt": ["GitHubDeployRole", "Arn"] } },

    "RedisEndpoint": { "Value": { "Fn::Sub": "redis.${ProjectName}.local:6379" } },
    "KafkaBootstrap": { "Value": { "Fn::Sub": "kafka.${ProjectName}.local:9092" } }
  }
```

---

##### Thông tin truy cập hệ thống

- **AlbDnsName**  
  DNS của Application Load Balancer.  
  Dùng để kiểm tra trực tiếp backend services qua HTTP.

- **HttpApiEndpoint**  
  Endpoint của API Gateway HTTP API.  
  Đây là điểm vào chính cho các request API từ frontend hoặc client bên ngoài.

- **CloudFrontDomain**  
  Domain của CloudFront distribution.  
  Dùng để truy cập frontend static website.

- **CloudFrontDistributionId**  
  ID của CloudFront distribution.  
  Dùng cho thao tác invalidate cache trong CI/CD pipeline.

---

##### Thông tin Database

- **Db1Endpoint**  
  Endpoint của database dùng cho User Service.

- **Db2Endpoint**  
  Endpoint của database dùng cho Notification Service và Chatbot Service.

- **Db3Endpoint**  
  Endpoint của database dùng cho Event Service.

Các database chỉ truy cập được từ bên trong VPC.

---

##### Thông tin lưu trữ S3

- **ImagesBucket**  
  Tên S3 bucket dùng để lưu trữ hình ảnh và file upload.

- **WebBucket**  
  Tên S3 bucket dùng để lưu trữ frontend static website.  
  Bucket được truy cập thông qua CloudFront.

---

##### Thông tin IAM

- **EcsTaskRoleArn**  
  ARN của IAM Role được ECS Task sử dụng.  
  Role này cho phép service truy cập các tài nguyên AWS như S3.

- **GitHubDeployRoleArn**  
  ARN của IAM Role dành cho GitHub Actions.  
  Role này được dùng để deploy frontend và backend thông qua OIDC.

---

##### Thông tin service nội bộ

- **RedisEndpoint**  
  Endpoint Redis nội bộ trong VPC.  
  Các backend service sử dụng để cache và session management.

- **KafkaBootstrap**  
  Bootstrap server của Kafka nội bộ.  
  Dùng cho giao tiếp bất đồng bộ giữa các microservices.

### Giai đoạn 5: CI/CD Pipeline (Tự động hóa build và triển khai)

Sau khi hạ tầng được triển khai bằng CloudFormation, hệ thống áp dụng CI/CD pipeline nhằm tự động hóa quá trình build, đóng gói và triển khai frontend và backend services lên môi trường AWS.

Pipeline được xây dựng bằng **GitHub Actions**, sử dụng **OIDC** để xác thực với AWS mà không cần lưu trữ access key tĩnh.

---

#### 5.1. Tổng quan CI/CD Pipeline

- Mô hình repository: **Mono‑repo**
- Mỗi microservice và frontend có [workflow](https://github.com/Heahaidu/interest-project/blob/main/.github/workflows/) riêng
- Pipeline được kích hoạt khi:
  - Push code lên nhánh `main`
  - Thay đổi trong thư mục tương ứng của service hoặc frontend

Pipeline đảm bảo:
- Build và deploy độc lập từng thành phần
- Giảm rủi ro ảnh hưởng chéo giữa các service
- Phù hợp với kiến trúc microservices

---

#### 5.2. CI/CD Pipeline cho Frontend (Next.js)

Frontend được build dưới dạng **static website** và triển khai lên **Amazon S3**, phân phối qua **CloudFront**.

**Luồng pipeline frontend:**

1. Checkout source code
2. Cài đặt Node.js và dependencies
3. Build ứng dụng Next.js với chế độ static export
4. Assume IAM Role trên AWS thông qua OIDC
5. Đồng bộ file build lên S3
6. Invalidate cache CloudFront

**Đặc điểm chính:**

- Không cần Docker cho frontend
- Không lưu thông tin AWS credential trong GitHub
- CloudFront đảm bảo người dùng luôn truy cập phiên bản mới nhất

Pipeline này cho phép frontend được cập nhật tự động ngay sau khi merge code.

---

#### 5.3. CI/CD Pipeline cho Backend Microservices

Mỗi backend service (User, Notification, Chatbot, Event) có workflow riêng nhưng cùng cấu trúc, do hệ thống sử dụng mono‑repo.

**Luồng pipeline backend service:**

1. Checkout source code
2. Assume IAM Role thông qua GitHub OIDC
3. Đăng nhập Amazon ECR
4. Build Docker image cho service
5. Push Docker image lên ECR với tag theo commit SHA
6. Lấy task definition hiện tại từ ECS
7. Cập nhật task definition với image mới
8. Deploy task definition mới lên ECS Service

---

#### 5.4. Tự động deploy ECS Service

- Mỗi microservice tương ứng với:
  - Một ECS Service
  - Một ECR repository
- ECS thực hiện rolling update khi có task definition mới
- Application Load Balancer đảm bảo:
  - Không gián đoạn dịch vụ
  - Chỉ route traffic đến các task healthy

Quá trình deploy diễn ra hoàn toàn tự động và không cần can thiệp thủ công.

---

#### 5.5. Bảo mật trong CI/CD Pipeline

- GitHub Actions sử dụng **OIDC** để assume IAM Role
- Không sử dụng access key hoặc secret tĩnh
- IAM Role cho pipeline chỉ có quyền:
  - Push image lên ECR
  - Cập nhật ECS Service
  - Deploy frontend lên S3 và CloudFront

Thiết kế này giảm rủi ro lộ thông tin xác thực và tuân theo nguyên tắc *least privilege*.

---

#### 5.6. Lợi ích của mô hình CI/CD

- Tự động hóa toàn bộ quy trình build và deploy
- Giảm lỗi triển khai thủ công
- Cho phép deploy độc lập từng microservice
- Phù hợp với kiến trúc cloud-native và microservices
- Dễ mở rộng khi bổ sung thêm service mới

---

#### 5.7. Tổng kết

Giai đoạn CI/CD Pipeline hoàn thiện chu trình phát triển và triển khai hệ thống, kết nối chặt chẽ giữa source code, hạ tầng CloudFormation và môi trường runtime trên AWS.

Thiết kế này giúp hệ thống duy trì tính ổn định, linh hoạt và sẵn sàng mở rộng trong tương lai.