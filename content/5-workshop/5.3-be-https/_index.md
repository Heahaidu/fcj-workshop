---
title : "HTTPS đối với Backend và SPA cho Frontend"
date :  "`r Sys.Date()`" 
weight : 3
pre: <b> 5.3 </b>
chapter : false
---

## Bật HTTPS cho Backend bằng CloudFront

Frontend chạy HTTPS. Trình duyệt chặn request từ trang HTTPS gọi sang API HTTP. Cần cung cấp endpoint HTTPS cho Backend. Không có chứng chỉ riêng nên dùng CloudFront để cung cấp HTTPS tạm thời cho Backend.

### Bước 1. Lấy API Gateway ID đúng từ stack

* Trong giao diện CloudFormation.

  * Chọn **Stacks**.  
  * Chọn stack đã tạo từ `infra.json`.  
  * Chọn **Outputs**.  
  * Tìm giá trị liên quan tới **API Gateway**.  
  * Ghi lại **API Gateway ID** hoặc **Invoke URL**.

![a](/5/34.png)

### Bước 2. Tạo Origin trỏ tới API Gateway
  * Truy cập **AWS Management Console**.

  * Tìm **CloudFront**.  
  * Chọn **CloudFront**.

  ![a](/5/35.png)

  * Trong giao diện CloudFront.

  * Chọn **Distributions**.  
  * Chọn đúng distribution đang dùng cho dự án.

  ![a](/5/36.png)


  * Vào tab **Origins**.

  * Chọn **Create origin**.
  ![a](/5/37.png)


  * Tại **Origin domain**.

  * Chọn **API Gateway**.  
  * Chọn đúng API có **ID trùng** với API Gateway đã tạo trong stack.

  ![a](/5/38.png)


  * Chọn **Create origin**.
  ![a](/5/39.png)


### Bước 3. Tạo Behavior cho đường dẫn API
Trong distribution vừa chọn.

Chọn tab **Behaviors**.  
Chọn **Create behavior**.

  ![a](/5/40.png)


Tại **Path pattern**.

Nhập `/api/*`.

Tại **Origin and origin groups**.

Chọn origin vừa tạo ở bước trước.

![a](/5/41.png)


Chọn **Create behavior**.

![a](/5/42.png)

## Thiết lập SPA routing cho Frontend bằng CloudFront Function
Truy cập **CloudFront Console**.

### Bước 1. Tạo CloudFront Function

Chọn **Functions**.  
Chọn **Create function**.

![a](/5/43.png)

Tại **Name**.

Nhập `interest-router` (hoặc tên bất kì).

Chọn **Create function**.

![a](/5/44.png)

Trong màn hình code editor.

Paste script router của SPA.
```javascript
function handler(event) {
  var request = event.request;
  var uri = request.uri;


  if (uri.startsWith('/api/')) {
    return request;
  }
  
  if (uri.includes('.')) {
    return request;
  }

  // Trang root
  if (uri === '/') {
    request.uri = '/index.html';
    return request;
  }

  if (uri.endsWith('/')) {
    request.uri = uri.slice(0,-1) + '.html';
    return request;
  }

  request.uri = uri + '.html';
  return request;
}
```

Chọn **Save changes**.

Chọn **Publish** nếu console yêu cầu publish để function có thể attach vào distribution.

![a](/5/45.png)

### Bước 2. Gắn Function vào default behavior của Frontend
Trong CloudFront.

Chọn **Distributions**.  
Chọn đúng distribution của dự án.

Chọn tab **Behaviors**.

Chọn behavior **Default**.  
Chọn **Edit**.

![a](/5/46.png)

Tại phần **Viewer request**.

Chọn **CloudFront Functions**.  
Tại **Function ARN/Name** chọn `interest-router`.

Chọn **Save changes**.

![a](/5/47.png)

Đợi CloudFront chuyển trạng thái function sang **Activated** trên distribution.

### Bước 3. Sử dụng endpoint HTTPS của CloudFront cho Backend
Dùng domain của CloudFront làm base URL cho Backend theo đường dẫn `/api`.

Ví dụ.

`https://<cloudfront-domain>/api/...`

Frontend gọi API qua HTTPS của CloudFront. Trình duyệt không còn chặn do mixed content.