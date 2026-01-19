---
title : "Blog 1"
date :  "`r Sys.Date()`" 
weight : 1
pre: <b> 3.1 </b>
chapter : false
---
# Bảo mật Amazon Elastic VMware Service (Amazon EVS) với AWS Network Firewall

> **📖 Bài viết gốc**: https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/  
> **👤 Tác giả**: Sheng Chen (Sr. Specialist Solutions Architect, AWS Australia) & Talha Kalim (Senior Systems Development Engineer, AWS)  
> **📅 Ngày xuất bản**: 26/11/2025  
> **🌐 Nguồn**: AWS Architecture Blog  
> **👨‍💻 Người dịch**: Nguyễn Hải Dương
> **📅 Ngày dịch**: 03/01/2026  
> **⏱️ Thời gian đọc**: 12 phút

---

## 📋 Tóm tắt

Amazon Elastic VMware Service (Amazon EVS) cho phép chạy VMware Cloud Foundation (VCF) trực tiếp trong Amazon VPC của bạn trên các máy chủ EC2 bare metal, giúp đẩy nhanh hành trình migrate và “thoát” data center mà không cần refactor ứng dụng. Trong bối cảnh kiến trúc hybrid cloud, bài viết trình bày cách xây dựng một mô hình **kiểm tra/soi lưu lượng tập trung (centralized inspection)** để bảo vệ lưu lượng giữa môi trường EVS, các VPC workload, on-premises và internet—với **một điểm quản trị policy**, log tập trung và quan sát tập trung.

Giải pháp sử dụng **AWS Network Firewall** (managed firewall + IDS/IPS) kết hợp **AWS Transit Gateway** qua cơ chế **tích hợp native** (native integration). Network Firewall đóng vai trò “bump-in-the-wire”: được chèn vào đường đi của lưu lượng bằng cách cập nhật route table, từ đó có thể lọc theo FQDN cho egress, kiểm soát ingress từ VPC Ingress (ALB) về EVS, và kiểm soát East-West giữa các VPC. Bài viết cũng cung cấp các bước triển khai chính (TGW, route tables, firewall attachment) và kịch bản kiểm thử bằng các stateful rule groups.

**🎯 Đối tượng đọc**: Kỹ sư Cloud/Network/Security triển khai hybrid networking trên AWS, có dùng VMware (VCF/NSX)  
**📊 Độ khó**: Intermediate (có chạm Advanced ở phần routing/TGW)  
**🏷️ Tags**: AWS, Amazon EVS, VMware Cloud Foundation, NSX, AWS Network Firewall, IDS/IPS, AWS Transit Gateway, Direct Connect, VPC, Centralized Inspection, Networking Security

---

## 📚 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Tổng quan kiến trúc](#tổng-quan-kiến-trúc)
- [Điều kiện tiên quyết](#điều-kiện-tiên-quyết)
- [Hướng dẫn triển khai (Walkthrough)](#hướng-dẫn-triển-khai-walkthrough)
  - [1) Tạo AWS Transit Gateway](#1-tạo-aws-transit-gateway)
  - [2) Gắn (attach) các VPC vào Transit Gateway](#2-gắn-attach-các-vpc-vào-transit-gateway)
  - [3) Tạo AWS Network Firewall với tích hợp native Transit Gateway](#3-tạo-aws-network-firewall-với-tích-hợp-native-transit-gateway)
  - [4) Cập nhật route table của Transit Gateway](#4-cập-nhật-route-table-của-transit-gateway)
  - [5) Cập nhật route table trong các VPC](#5-cập-nhật-route-table-trong-các-vpc)
  - [6) Kiểm thử](#6-kiểm-thử)
    - [Kiểm thử egress (lọc FQDN)](#kiểm-thử-egress-lọc-fqdn)
    - [Kiểm thử ingress](#kiểm-thử-ingress)
    - [Kiểm thử East–West](#kiểm-thử-eastwest)
- [Kết luận](#kết-luận)
- [Về tác giả](#về-tác-giả)
- [Glossary - Thuật ngữ](#-glossary---thuật-ngữ)
- [Tài liệu tham khảo](#-tài-liệu-tham-khảo)
- [Ghi chú của người dịch](#-ghi-chú-của-người-dịch)
- [Đóng góp và Feedback](#-đóng-góp-và-feedback)

---

## Giới thiệu

[Amazon Elastic VMware Service (Amazon EVS)](https://aws.amazon.com/evs/) giúp các tổ chức **di chuyển (migrate), vận hành và mở rộng (scale)** các workload VMware **một cách cục bộ trên AWS**. Dịch vụ cung cấp môi trường [VMware Cloud Foundation (VCF)](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/getting-started-with-vcf-5-2/natively-integrated-stack.html) hoạt động trực tiếp trong [Amazon Virtual Private Cloud (Amazon VPC)](https://aws.amazon.com/vpc/) của bạn trên các [Amazon EC2 bare-metal instances](https://aws.amazon.com/ec2/). Nhờ đó, khách hàng có thể tăng tốc migrate lên cloud và rời data center mà **không cần refactor** các ứng dụng hiện có.

Với các khách hàng cân nhắc kiến trúc hybrid cloud, cần có một giải pháp bảo mật mạng thống nhất để bảo vệ lưu lượng ứng dụng đi qua các môi trường Amazon EVS, các Amazon VPC, data center on-premises và internet. Giải pháp này cũng cần cung cấp **một điểm điều khiển duy nhất** cho việc quản lý policy tường lửa, **logging** và **monitoring** tập trung nhằm đơn giản hóa vận hành bảo mật mạng.

[AWS Network Firewall](https://aws.amazon.com/network-firewall/) là dịch vụ tường lửa được quản lý (managed) kết hợp **phát hiện/ngăn chặn xâm nhập (IDS/IPS)**, có thể đáp ứng các yêu cầu trên. Dựa trên hạ tầng do AWS quản lý, dịch vụ tự động scale theo nhu cầu lưu lượng, đồng thời duy trì **tính sẵn sàng cao** và hiệu năng ổn định. AWS Network Firewall cung cấp quản lý policy tập trung và kiểm tra lưu lượng (traffic inspection) trên nhiều VPC và nhiều tài khoản AWS. Ngoài ra, dịch vụ cung cấp khả năng quan sát và báo cáo toàn diện thông qua việc thu thập log về [Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/), [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) hoặc [Amazon Data Firehose](https://aws.amazon.com/firehose/).

Trong bài viết này, chúng tôi trình bày cách sử dụng AWS Network Firewall để bảo mật môi trường Amazon EVS, dựa trên kiến trúc kiểm tra tập trung (centralized inspection) áp dụng cho: EVS cluster, các VPC, on-premises và internet. Bài viết sẽ đi qua các bước triển khai kiến trúc này bằng AWS Network Firewall và [AWS Transit Gateway](https://aws.amazon.com/transit-gateway/).

---

## Tổng quan kiến trúc

AWS Network Firewall hoạt động như một giải pháp **“bump-in-the-wire”**: kiểm tra và lọc lưu lượng một cách “trong suốt” (transparent) trên Amazon VPC. Dịch vụ được chèn trực tiếp vào đường đi của lưu lượng bằng cách cập nhật **VPC route table** hoặc **Transit Gateway route table**, cho phép kiểm tra tất cả packet **mà không cần thay đổi** các mẫu luồng (flow patterns) hiện có của ứng dụng.

Sơ đồ sau minh họa tổng quan kiến trúc mô hình kiểm tra tập trung sử dụng AWS Network Firewall.

![Hình 1: Bảo mật Amazon EVS với AWS Network Firewall theo kiến trúc kiểm tra tập trung](/images/ARCHBLOG-1250-Figure1.png)

Môi trường Amazon EVS được triển khai trực tiếp bên trong VPC của khách hàng (tức EVS VPC), bao gồm các [EVS VLAN subnet](https://docs.aws.amazon.com/evs/latest/userguide/concepts.html#concepts-evs-network) tạo thành underlay network cho triển khai VCF. Hạ tầng này cung cấp kết nối cho các mạng NSX overlay, quản trị host, [vMotion](https://blogs.vmware.com/cloud-foundation/2019/07/09/the-vmotion-process-under-the-hood/) và [vSAN](https://www.vmware.com/products/cloud-infrastructure/vsan). [Amazon VPC Route Server](https://docs.aws.amazon.com/vpc/latest/userguide/dynamic-routing-route-server.html) cho phép định tuyến động giữa underlay network và overlay network. Để biết thêm, tham khảo mục [Concepts and components of Amazon EVS](https://docs.aws.amazon.com/evs/latest/userguide/concepts.html) trong tài liệu.

Kiến trúc cũng bao gồm một workload VPC tiêu chuẩn (tức VPC01), và một [Direct Connect Gateway](https://docs.aws.amazon.com/directconnect/latest/UserGuide/direct-connect-gateways-intro.html) kết nối tới data center on-premises thông qua một kết nối [AWS Direct Connect](https://aws.amazon.com/directconnect/). Chúng tôi sử dụng một egress VPC chuyên dụng với [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) để tập trung egress ra internet, và một ingress VPC riêng với [Application Load Balancer (ALB)](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/) để terminate lưu lượng web ingress và điều hướng luồng quay về các dịch vụ đích.

Với kiến trúc này, có thể kiểm tra các mẫu luồng lưu lượng sau:

**Lưu lượng East–West (đông–tây):**
- Giữa EVS VPC và các Workload VPC
- Giữa các Workload VPC

**Lưu lượng North–South (bắc–nam):**
- Giữa EVS/Workload VPC và on-premises
- Giữa EVS/Workload VPC và internet
- Giữa on-premises và internet

Kiến trúc kiểm tra tập trung mang lại các lợi ích:
- **Một điểm điều khiển** để kiểm tra bảo mật mạng trên nhiều VPC
- Tăng cường thực thi rule trên hạ tầng AWS, tài nguyên on-premises và internet
- Logging và monitoring tập trung

Trong demo này, chúng tôi dùng khả năng [tích hợp native giữa AWS Network Firewall và AWS Transit Gateway](https://aws.amazon.com/about-aws/whats-new/2025/07/aws-network-firewall-native-transit-gateway-support/) để đơn giản hóa việc triển khai và quản trị tường lửa. Với **native firewall attachment**, AWS tự động provision và quản lý các tài nguyên VPC cần thiết, giúp giảm overhead vận hành (không phải tự quản subnets, route tables và firewall endpoints trong inspection VPC).

---

## Điều kiện tiên quyết

Bài viết giả định bạn đã quen thuộc với: [AWS Command Line Interface (AWS CLI)](https://aws.amazon.com/cli/), Amazon VPC, Amazon EC2, NAT gateway, Application Load Balancer, [Internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#vpc-igw-internet-access), AWS Direct Connect, AWS Transit Gateway và nền tảng VMware VCF.

Các điều kiện tiên quyết cần có để hoàn thành giải pháp này:

**EVS VPC bao gồm:**
- Một Amazon EVS cluster (tối thiểu **4x i4i nodes**)
- VPC CIDR: `10.0.0.0/16`
- NSX Segments CIDR: `192.168.0.0/19` (đã tổng hợp – summarized)
- Một **VPC Route Server** triển khai trong EVS VPC để nhận route của NSX segment thông qua định tuyến động BGP. Tham khảo **EVS User Guide** để biết thêm chi tiết.

**Workload VPC (VPC01):**
- CIDR: `172.21.0.0/16`

**Egress VPC:**
- CIDR: `172.23.0.0/16`
- 1x Internet Gateway
- 1x NAT Gateway

**Ingress VPC:**
- CIDR: `172.24.0.0/16`
- 1x Internet Gateway
- 1x Application Load Balancer

**Tùy chọn: Direct Connect Gateway**
- Kết nối tới môi trường on-premises (`10.0.0.0/8`)

> **Lưu ý**: Các CIDR dùng trong ví dụ chỉ phục vụ demo, hãy thay bằng dải địa chỉ phù hợp với môi trường của bạn. Thiết kế cũng có thể mở rộng để bao gồm thêm các môi trường EVS và/hoặc thêm VPC khác tùy nhu cầu workload.

---

## Hướng dẫn triển khai (Walkthrough)

Trong phần này, chúng tôi sẽ đi qua các bước triển khai kiến trúc kiểm tra tập trung với AWS Network Firewall và AWS Transit Gateway. Chúng tôi tập trung vào tích hợp mạng tổng thể của kiến trúc, **không** đi sâu vào cấu hình chi tiết của AWS Network Firewall hay Transit Gateway.

### 1) Tạo AWS Transit Gateway

Trong VPC Console, tạo một **Transit Gateway**. Đảm bảo **bỏ chọn** các tùy chọn sau:

- **Default route table association**
- **Default route table propagation**

Tạo **hai** Transit Gateway route table (trống) và associate với Transit Gateway:

- **Pre-inspection route table**: điều hướng lưu lượng đi vào AWS Network Firewall để kiểm tra tập trung
- **Post-inspection route table**: trả lưu lượng về đúng đích ban đầu sau khi đã được kiểm tra và được AWS Network Firewall cho phép

### 2) Gắn (attach) các VPC vào Transit Gateway

Gắn cả bốn VPC (**EVS**, **VPC01**, **Ingress**, **Egress**) vào cùng một Transit Gateway. **Direct Connect Gateway** cũng có thể được attach vào Transit Gateway nếu cần AWS Network Firewall kiểm tra lưu lượng giữa on-premises và AWS hoặc internet.

![Hình 2: Gắn các VPC vào Transit Gateway](/images/ARCHBLOG-1250-Figure2.png)

Associate tất cả các attachment vào **pre-inspection Transit Gateway route table**.

![Hình 3: Associate các VPC attachment vào pre-inspection route table](/images/ARCHBLOG-1250-Figure3.png)

### 3) Tạo AWS Network Firewall với tích hợp native Transit Gateway

Trong mục **Network Firewall** của VPC Console, chọn **Create firewall**.

Ở phần **Attachment type**, chọn **Transit Gateway** để bật tích hợp native với Transit Gateway hiện có.

![Hình 4: Bật AWS Network Firewall native integration với Transit Gateway](/images/ARCHBLOG-1250-Figure4.png)

Ở phần **Logging configuration**, bật các loại log sau và chọn **CloudWatch log group** làm nơi nhận log. Tạo một log group cho mỗi loại trong CloudWatch Console:

- **Alert**: `/anfw-centralized/anfw01/alert`
- **Flow**: `/anfw-centralized/anfw01/flow`

Tạo và associate một **firewall policy rỗng** để triển khai AWS Network Firewall instance. Firewall policy chứa danh sách các **rule group** định nghĩa cách tường lửa kiểm tra và xử lý lưu lượng. Firewall policy rỗng này có thể cấu hình sau.

Khi bật tích hợp native với Transit Gateway, một Transit Gateway attachment sẽ được tự động tạo cho AWS Network Firewall, với **resource type** hiển thị là **Network Function**. Ngoài ra, **Appliance Mode** cũng được tự động bật cho firewall attachment để đảm bảo Transit Gateway tiếp tục sử dụng cùng một **Availability Zone (AZ)** cho attachment trong suốt vòng đời của một flow.

Associate firewall attachment vào **post-inspection Transit Gateway route table**.

![Hình 5: AWS Network Firewall native attachment](/images/ARCHBLOG-1250-Figure5.png)

### 4) Cập nhật route table của Transit Gateway

Cập nhật **pre-inspection Transit Gateway route table** bằng cách thêm một **default route** trỏ tới AWS Network Firewall attachment. Việc này đảm bảo mọi lưu lượng đi vào Transit Gateway từ các VPC attachment và Direct Connect Gateway attachment sẽ được gửi tới tường lửa để kiểm tra tập trung.

![Hình 6: Transit Gateway pre-inspection route table](/images/ARCHBLOG-1250-Figure6.png)

Thêm các **static route** sau vào **post-inspection route table** để điều hướng lưu lượng chiều về (return traffic) về đúng từng VPC và Direct Connect Gateway tương ứng.

![Hình 7: Transit Gateway post-inspection route table](/images/ARCHBLOG-1250-Figure7.png)

### 5) Cập nhật route table trong các VPC

Cuối cùng, cập nhật route table ở mỗi VPC theo bảng trong bài gốc.

Đảm bảo thêm các route sau vào các VPC route table liên quan:

- **EVS VPC** và **VPC01** có **default route** (được đánh dấu màu xanh dương trong bài gốc) để điều hướng toàn bộ egress flow vào AWS Network Firewall nhằm kiểm tra tập trung.
- **Ingress VPC** và **Egress VPC** có các route **RFC-1918** (được đánh dấu màu xanh lá trong bài gốc) để điều hướng lưu lượng chiều về quay lại Transit Gateway.

Trong EVS VPC, lưu ý rằng các route của **NSX segment** được tự động propagate tới route table của **NSX uplink subnet** và **private subnet** thông qua **VPC Route Server**.

![Hình 8: Route table của NSX uplink subnet trong EVS VPC](/images/ARCHBLOG-1250-Figure8.png)

Đến đây, kiến trúc kiểm tra bảo mật tập trung đã được triển khai cho môi trường EVS, sử dụng AWS Network Firewall với tích hợp native Transit Gateway.

### 6) Kiểm thử

#### Kiểm thử egress (lọc FQDN)

Để kiểm thử egress inspection từ EVS VPC hoặc VPC01 ra internet, tạo một **stateful rule group** cho firewall instance bằng **FQDN filtering**:

- **Rule group format**: Domain list
- **Domain names**: `.google.com`
- **Source IPs**: `192.168.0.0/19`, `172.21.0.0/16`
- **Protocols**: HTTP & HTTPS
- **Action**: Allow

Đúng như kỳ vọng, khi kiểm tra truy cập web từ một máy ảo (`192.168.12.10`) trong môi trường EVS tới domain được phép (google.com) thì lưu lượng được AWS Network Firewall cho qua. Tuy nhiên, truy cập tới domain không được phép (facebook.com) sẽ bị chặn tại tường lửa và phát sinh alert, có thể kiểm chứng trong CloudWatch log group tại `/aws/network-firewall/alert/`.

![Hình 9: Egress inspection từ EVS ra internet với FQDN filtering](/images/ARCHBLOG-1250-Figure9.jpg)

#### Kiểm thử ingress

Tạo thêm một **stateful rule group** để cho phép Application Load Balancer triển khai trong Ingress VPC truy cập tới một web server chạy trong môi trường EVS qua giao thức HTTP:

- **Rule group format**: Standard stateful rule
- **Geographic IP Filtering**: Disable Geographic IP filtering
- **Protocol**: HTTP
- **Source**: `172.24.0.0/16`
- **Source Port**: ANY
- **Destination**: `192.168.12.10/32`
- **Destination Port**: ANY
- **Traffic direction**: Forward
- **Action**: Alert

Firewall logs trên CloudWatch cho thấy một Application Load Balancer (`172.24.6.45`) từ Ingress VPC có thể thiết lập kết nối HTTP tới EVS web server (`192.168.12.10`). Ngoài ra, Application Load Balancer cũng đã đăng ký thành công EVS web server như một **remote IP target**.

![Hình 10: Ingress inspection từ Ingress VPC tới EVS](/images/ARCHBLOG-1250-Figure10.png)

#### Kiểm thử East–West

Để kiểm thử East–West inspection, cập nhật stateful rule group trước đó để bổ sung một rule mới chặn lưu lượng ICMP từ VPC01 tới EVS VPC:

- **Rule group format**: Standard stateful rule
- **Geographic IP Filtering**: Disable Geographic IP filtering
- **Protocol**: ICMP
- **Source**: `172.21.0.0/16`
- **Source Port**: ANY
- **Destination**: `192.168.0.0/19`
- **Destination Port**: ANY
- **Action**: Drop

Kết quả: các gói ping từ một EC2 instance (`172.21.128.4`) ở VPC01 tới EVS web server (`192.168.12.10`) bị drop.

![Hình 11: East–West inspection từ VPC01 tới EVS](/images/ARCHBLOG-1250-Figure11.jpg)

---

## Kết luận

Trong bài viết này, chúng tôi đã trình bày cách sử dụng AWS Network Firewall để bảo mật các workload Amazon EVS và cung cấp cơ chế kiểm tra lưu lượng tập trung giữa các môi trường Amazon EVS, các Amazon VPC, on-premises và internet. Chúng tôi cũng đã đi qua các bước triển khai kiến trúc kiểm tra tập trung bằng AWS Network Firewall và AWS Transit Gateway.

Để tìm hiểu thêm, hãy tham khảo các tài nguyên sau:
- **Amazon Elastic VMware Service (Amazon EVS) User Guide**
- **AWS Blog**: Introducing Amazon Elastic VMware Service for running VMware Cloud Foundation on AWS
- **AWS Blog**: Deployment models for AWS Network Firewall

---

## Về tác giả

### Sheng Chen
Sheng Chen là **Sr. Specialist Solutions Architect** tại **AWS Australia**, có hơn 20 năm kinh nghiệm về hạ tầng CNTT, kiến trúc cloud và networking đa đám mây. Trong vai trò hiện tại, Sheng hỗ trợ khách hàng tăng tốc cloud migration và hiện đại hóa ứng dụng bằng các công nghệ cloud-native. Mối quan tâm gần đây của anh bao gồm dịch vụ hybrid cloud của AWS, Amazon EKS, platform engineering và hạ tầng AI.

### Talha Kalim
Talha Kalim có hơn 10 năm kinh nghiệm về hạ tầng doanh nghiệp và giải pháp hybrid cloud. Anh đã thiết kế và triển khai các kiến trúc data center có khả năng chịu lỗi cao (resilient) cho nhiều ngành. Là **Senior Systems Development Engineer** tại **Amazon Web Services (AWS)**, anh đảm nhiệm vai trò technical leadership cho **Amazon Elastic VMware Service (Amazon EVS)**—một dịch vụ AWS cho phép khách hàng chạy VMware Cloud Foundation trên hạ tầng cloud của AWS. Chuyên môn của Talha trải rộng từ các công nghệ ảo hóa VMware như vSphere và NSX đến hiểu biết sâu về các dịch vụ AWS, phản ánh bề dày kinh nghiệm của anh trong kiến trúc hybrid cloud.

---

## 📖 Glossary - Thuật ngữ

| English | Tiếng Việt | Định nghĩa |
|---|---|---|
| Amazon EVS (Amazon Elastic VMware Service) | Dịch vụ VMware co giãn trên AWS | Dịch vụ giúp chạy VMware Cloud Foundation trực tiếp trong VPC trên EC2 bare metal. |
| VMware Cloud Foundation (VCF) | Nền tảng VMware Cloud Foundation | Bộ stack VMware (vSphere/vSAN/NSX + quản trị) để xây dựng private cloud/hybrid cloud. |
| Amazon VPC | Mạng riêng ảo trên AWS | Không gian mạng logic cô lập, nơi triển khai subnet/route/SG/NACL… |
| Transit Gateway (TGW) | Cổng trung chuyển | Hub định tuyến kết nối nhiều VPC/on-prem (Direct Connect/VPN) theo mô hình hub-and-spoke. |
| AWS Network Firewall | Tường lửa mạng AWS | Managed firewall + IDS/IPS, hỗ trợ policy tập trung và logging/monitoring. |
| IDS/IPS | Phát hiện/Ngăn chặn xâm nhập | Cơ chế phát hiện và chặn hành vi tấn công dựa trên signature/heuristic. |
| Centralized inspection | Kiểm tra tập trung | Gom lưu lượng qua một lớp firewall/inspection chung thay vì phân tán ở từng VPC. |
| Attachment | Kết nối (attachment) | Liên kết giữa VPC/Direct Connect Gateway/Network Function với Transit Gateway. |
| Route table | Bảng định tuyến | Tập các rule xác định next hop cho từng dải đích (CIDR). |
| Propagation | Lan truyền route | Cơ chế tự động học/đưa route từ attachment vào route table TGW. |
| Appliance mode | Chế độ thiết bị (Appliance Mode) | Cơ chế TGW giúp “giữ AZ” để đảm bảo tính đối xứng lưu lượng cho appliance. |
| Ingress / Egress | Lưu lượng vào / ra | Ingress: từ ngoài vào hệ thống, Egress: từ hệ thống ra ngoài (ví dụ internet). |
| East–West traffic | Lưu lượng đông–tây | Lưu lượng ngang giữa các workload nội bộ (VPC-to-VPC, segment-to-segment). |
| North–South traffic | Lưu lượng bắc–nam | Lưu lượng giữa nội bộ và bên ngoài (on-prem/internet). |
| NAT Gateway | Cổng NAT | Dịch vụ NAT quản lý giúp subnet private truy cập internet (IPv4) mà không public IP trực tiếp. |
| Internet Gateway (IGW) | Cổng internet | Kết nối VPC với internet cho các subnet có route phù hợp. |
| Application Load Balancer (ALB) | Cân bằng tải ứng dụng | Load balancer L7 (HTTP/HTTPS), hỗ trợ routing theo host/path, target group, health check. |
| FQDN filtering | Lọc theo FQDN | Cho phép/chặn dựa trên tên miền đầy đủ (ví dụ `.google.com`) cho HTTP/HTTPS. |
| Stateful rule group | Nhóm rule có trạng thái | Rule theo dõi trạng thái kết nối (connection tracking) để quyết định allow/alert/drop. |
| RFC-1918 | Dải IP private | Các dải IPv4 private: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16. |
| BGP | Giao thức định tuyến biên | Giao thức định tuyến động thường dùng để trao đổi route giữa hệ thống/miền định tuyến. |
| Underlay / Overlay | Mạng nền / mạng phủ | Underlay: hạ tầng mạng vật lý/logic bên dưới, Overlay: mạng ảo hóa chạy “phủ” bên trên (NSX). |

---

## 🔗 Tài liệu tham khảo

### Tài liệu gốc
- [Original Article](https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/): Bài viết gốc
- [AWS Architecture Blog](https://aws.amazon.com/blogs/architecture/): Chuyên trang kiến trúc của AWS

### Tools và Services
- [AWS Network Firewall – Developer Guide](https://docs.aws.amazon.com/network-firewall/latest/developerguide/what-is-aws-network-firewall.html): Mô tả service & hướng dẫn cấu hình
- [AWS Transit Gateway – Documentation](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html): Tổng quan & hướng dẫn
- [AWS Direct Connect – User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html): Hướng dẫn Direct Connect
- [Elastic Load Balancing – ALB](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html): Giới thiệu ALB
- [Amazon VPC – NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html): NAT Gateway

---

## 🤝 Đóng góp và Feedback

Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**.

**📧 Liên hệ**: nguyenhaiduong20004@gmail.com   
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên  
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng
