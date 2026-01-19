---
title : "Các bước chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 2
pre: <b> 5.2 </b>
chapter : false
---

## Khởi tạo tài nguyên bằng CloudFormation
Trong lab này, chúng ta sẽ dùng N.Virginia region `us-east-1`.

1. Truy cập **AWS Management Console**.

   * Chọn region **N. Virginia (us-east-1)**.
   * Tìm **CloudFormation**.
   * Chọn **CloudFormation**.

   ![a](/5/1.png)

2. Tại giao diện CloudFormation.

   * Chọn **Stacks**.
   * Chọn **Create stack**.
   * Chọn **With new resources (standard)**.

    ![a](/5/2.png)

3. Chuẩn bị template.

   * Tải file [infra.json](/5/infra.json) về máy.
   * Đặt file ở thư mục dễ tìm.
   * Sử dụng **Ctrl + S** để lưu nếu đang mở từ trình duyệt.

4. Trong Create stack.

   * Chọn **Template is ready**.
   * Chọn **Upload a template file**.
   * Chọn **Choose file**.
   * Chọn file `infra.json`.
   * Chọn **Next**.
    ![a](/5/3.png)
5. Tại Specify stack details.

   * **Stack name:** nhập tên stack (tùy ý).

   * Nhập các **Parameters**.

     * **ProjectName:** nhập tên dự án.  
       Ví dụ `interest`.

     * **BootstrapMode:** chọn `true`.

     * **BootstrapImage:** nhập `heahaidu/springboot-health-bootstrap:latest`.

     * **MailHost:** nhập SMTP host.  
       Ví dụ `smtp.gmail.com` hoặc SMTP endpoint của SES theo region.

     * **MailUsername:** nhập SMTP username.  
       Ví dụ địa chỉ email hoặc SES SMTP username.

     * **MailPassword:** nhập SMTP password.  
       Ví dụ [Gmail App Password](https://support.google.com/accounts/answer/185833?hl=en) hoặc SES SMTP password.

     * **DbPassword:** nhập mật khẩu database user ứng dụng sẽ dùng để kết nối.

   * Chọn **Next**.

6. Tại Configure stack options.

   * Giữ mặc định nếu không có yêu cầu riêng.
   * Tick chọn **I acknowledge that AWS CloudFormation might create IAM resources**.
   * Chọn **Next**.

   ![a](/5/8.png)

7. Tại Review.

   * Chọn **Submit**.

   ![a](/5/9.png)


8. Theo dõi quá trình tạo stack.

   * Chọn stack vừa tạo.
   * Chọn tab **Events** để xem tiến trình.
   * Đợi trạng thái là **CREATE_COMPLETE**.

   ![a](/5/10.png)

9. Kiểm tra ALB

   * Tại stack vừa tạo chọn Resource.
   * Tìm kiếm Logical ID là **Alb** đi tới Console của ALB thông qua đường dẫn tại **Physical ID**.

   ![a](/5/11.png)

   * Tại console của **Alb** vừa tạo chọn **Resource map** để kiểm tra các task khỏe.
    ![a](/5/12.png)
    ![a](/5/13.png)

## Thiết lập biến môi trường cho Github Action

1. Lấy Outputs để cấu hình GitHub.
   * Trở lại Stack Cloudformation.
   * Chọn tab **Outputs**.

    ![a](/5/14.png)
    ![a](/5/15.png)

   * Sao chép các giá trị output cần dùng tương đương.
     * `AWS_REGION` = `Region`
     * `PROJECT_NAME` = `ProjectName`
     * `SERVICE_A_NAME` = `ServiceAName`
     * `SERVICE_B_NAME` = `ServiceBName`
     * `SERVICE_C_NAME` = `ServiceCName`
     * `SERVICE_D_NAME` = `ServiceDName`
     * `S3_BUCKET_FE` = `S3BucketFe`
     * `CLOUDFRONT_DIST_ID` = `CloudFrontDistributionId`
     * `AWS_ROLE_ARN` = `GitHubDeployRoleArn`

2. Tiến hành thêm biến môi trường để setup cho Github Action     

    * Tại Project Repository chọn `Settings` → `Secrets and variables` → `Actions`.
    ![a](/5/16.png)
    ![a](/5/17.png)

    * Tại **Actions secrets and variables** → **Secrets** → **New repository secret** thêm **AWS_ROLE_ARN** và secret → Add secret.
    ![a](/5/18.png)
    ![a](/5/19.png)

    * Chuyển sang **Variable** → Lần lượt nhập nhập các ENV còn lại.

    ![a](/5/20.png)
    ![a](/5/21.png)

## Update stack CloudFormation

1. Quay trở lại stack đã tạo bên trên
    * Chọn **Update stack** → **Make a direct update**.
    ![a](/5/22.png)

    * Tại **Update stack** chọn **Use existing template** → Next
    ![a](/5/23.png)

2. Tại Specify stack details.

    * Chuyển **BootstrapMode** sang thành `false`
    ![a](/5/24.png)
    * Chọn **Next**.

6. Tại Configure stack options.

   * Giữ mặc định nếu không có yêu cầu riêng.
   * Tick chọn **I acknowledge that AWS CloudFormation might create IAM resources**.
   * Chọn **Next**.

   ![a](/5/25.png)

7. Tại Review.

   * Chọn **Submit**.

   ![a](/5/26.png)

8. Chờ đợi.
   * Chờ đợi cho tới khi chỉ còn 4 task (ServiceA → D) có status là *UPDATE_IN_PROGRESS*
   ![a](/5/27.png)

## Triển khai CICD Pipeline qua Github Actions

1. Tạo trigger cho các actions file config.
> Tại repo này *Actions* sẽ được tự động kích hoạt khi repo của config đó thay đổi hoặc config đó thay đổi. 

   ![a](/5/28.png)
   Ví dụ tại config actions của user-service (các service khác cũng tương tự).

2. Sau khi tạo trigger thành công.
> Github Actions sẽ tự động build và push lên ECR đối với BE và S3 đối với FE thông qua *AWS_ROLE_ARN*
   ![a](/5/29.png)
   ![a](/5/30.png)

3. Kiểm tra ALB

   * Quay lại ALB.
   * Tại console của **Alb** vừa tạo chọn **Resource map** để kiểm tra các task khỏe (nhớ làm mới lại trang).
   * Nếu không có đối tượng nào **Unhealth** thì thành công
    ![a](/5/31.png)

## Giải thích cơ chế hoạt động

### BootstrapMode điều khiển giá trị Image trong task definition

Template tạo ECS task definition cho từng service.  
Mỗi task definition có trường `Image`.  
`BootstrapMode=true` chọn image trên Docker Hub ở đây.  
`BootstrapMode=false` chọn image trên ECR theo tên repo và tag (ở đây là `heahaidu/springboot-health-bootstrap:latest`).

CloudFormation chỉ tạo ra cấu hình.  
ECS mới là hệ thống chạy task theo cấu hình đó.

### Tại sao bootstrap image giúp stack tạo thành công

Khi stack tạo lần đầu, ECR chưa có image.  
Nếu task definition trỏ vào ECR ngay, ECS không pull được image.  
Service không ổn định.  
CloudFormation không thể hoàn tất tạo stack.

Bootstrap image tồn tại sẵn trên Docker Hub.  
ECS pull được image ngay.  
Task chạy lên.  
Target group có endpoint trả 200 theo health check.  
Service đạt trạng thái ổn định.  
CloudFormation hoàn tất tạo stack và xuất Outputs.

### Outputs là dữ liệu cấu hình cho GitHub Actions

Template xuất Outputs như region và tên tài nguyên.  
GitHub Actions cần các giá trị này để gọi AWS API đúng region.  
GitHub Actions cần các giá trị này để tạo đúng tên ECR repo và tham chiếu đúng service cần cập nhật.  
GitHub Actions cần role ARN để lấy quyền qua OIDC và thao tác với ECR và các dịch vụ liên quan.

Không có Outputs thì Actions thiếu dữ liệu định danh tài nguyên.  
Actions không thể build và push vào đúng nơi một cách tự động.

### Khi chuyển sang ECR, ECS cố chạy revision mới ngay lập tức

Update stack với `BootstrapMode=false` tạo task definition revision mới.  
Revision mới trỏ sang ECR image URI.

ECS service nhìn thấy task definition mới.  
ECS bắt đầu triển khai revision mới theo chiến lược rolling update.  
ECS tạo task mới và yêu cầu agent pull image từ ECR.

Nếu ECR chưa có image đúng tag, pull thất bại.  
Task mới không vào trạng thái running.  
Service không đạt trạng thái ổn định.

CloudFormation theo dõi trạng thái ổn định của ECS service.  
Nếu service chưa ổn định, CloudFormation giữ trạng thái update.

### Vì sao push image sau đó làm update thành công

GitHub Actions build image và push lên đúng ECR repo và tag.  
ECR tạo manifest cho tag đó.

ECS tiếp tục cố gắng tạo task theo revision mới.  
Lần pull sau thành công vì image đã tồn tại.  
Task chạy lên và vượt health check của target group.  
Số task healthy đạt mức service yêu cầu.

Khi ECS service báo ổn định, CloudFormation hoàn tất update.

### Vai trò của 5 cấu hình GitHub Actions

Mỗi cấu hình backend build một image và push lên ECR repo tương ứng.  
Cấu hình frontend build và triển khai lên S3 và CloudFront.

Thay đổi cấu hình có thể kích hoạt build đồng thời.  
Khi đủ image backend xuất hiện trên ECR, ECS service có thể ổn định ở revision mới.