---
title : "Tạo Launch Template, Auto Scaling Group, Load Balancer"
date :  "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---
## Giới thiệu

Trong AWS, việc quản lý khả năng mở rộng, tính sẵn sàng và hiệu quả của ứng dụng bao gồm việc sử dụng nhiều dịch vụ khác nhau. Đáng chú ý trong số này là **Launch Templates**, **Auto Scaling Groups (ASGs)**, và **Load Balancers**. Cùng nhau, chúng cung cấp một cơ chế mạnh mẽ để triển khai và quản lý ứng dụng một cách linh hoạt và đáng tin cậy.

- **Launch Template**: Launch Template là một mẫu cấu hình phiên bản mà bạn có thể sử dụng để khởi tạo một phiên bản EC2. Nó cho phép bạn chỉ định Amazon Machine Image (AMI), loại phiên bản, cặp khóa, nhóm bảo mật và các tham số khác.

- **Auto Scaling Group (ASG)**: Auto Scaling Group quản lý một nhóm các phiên bản EC2 có cùng đặc điểm. ASG đảm bảo rằng ứng dụng của bạn có thể tự động mở rộng hoặc thu hẹp theo nhu cầu và duy trì một số lượng phiên bản khỏe mạnh cố định.

- **Load Balancer**: Load Balancer tự động phân phối lưu lượng truy cập ứng dụng đến nhiều mục tiêu, như các phiên bản EC2, container, hoặc địa chỉ IP. Điều này giúp cải thiện tính sẵn sàng và khả năng chịu lỗi của ứng dụng.

## Mục tiêu

Hướng dẫn này sẽ giúp bạn thực hiện các bước sau:

1. Tạo Launch Template để định nghĩa cấu hình phiên bản.
2. Thiết lập Auto Scaling Group và Load Balancer sử dụng Launch Template.
3. Cập nhật lại Target Group

## Các bước thực hiện

### Bước 1: Tạo Launch Template

1. Đăng nhập vào **AWS Management Console**.
2. Điều hướng đến **EC2** bằng cách tìm `EC2` trong thanh tìm kiếm.
3. Ở bảng điều khiển bên trái, nhấp vào **Launch Templates**.
4. Nhấp vào **Create Launch Template**.
5. Nhập **Launch Template Name** và **Description** (mô tả) tùy chọn.
![Template-Name](/images/2-TaskList/2.4-CreateASG/Template-Name.png)
6. Chọn **Amazon Machine Image (AMI)** là `Amazon Linux 2 AMI`.
![Instance-type](/images/2-TaskList/2.4-CreateASG/instance-type.png)
7. Chọn **Instance Type** là `t2.micro` (Free tier eligible).
8. Trong phần **Create key Pair**, chọn `Create new key pair`, điền Key pair name và chọn **Create key pair**
![Key-Pair](/images/2-TaskList/2.4-CreateASG/key-pair.png)
9.  Trong **Network Settings**, chọn **Create security group**, chọn **VPC** đã tạo ở bước trước đó, thêm 2 **Inbound Security Group Rules** như hình dưới.
![security-group](/images/2-TaskList/2.4-CreateASG/security-group.png)
10. Trong **Advanced Details**, thêm vào **User Data** nội dung sau với mục đích cài đặt môi trường cũng như Deploy agent trên instance:
```sh
#!/bin/bash
sudo yum update -y
sudo yum install java-17-amazon-corretto-headless -y
sudo yum install maven -y

sudo yum install -y ruby
cd /home/ec2-user
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto

sudo service codedeploy-agent start
```
11. Nhấp vào **Create Launch Template**.

### Bước 2: Thiết lập Auto Scaling Group và Load Balancer sử dụng Launch Template

1. Điều hướng đến phần **Auto Scaling Groups** trong bảng điều khiển **EC2**.
2. Nhấp vào **Create Auto Scaling group**.
3. Nhập **Auto Scaling group name**.
4. Trong **Launch Template**, chọn **Launch Template** đã tạo ở Bước 1.
5. Nhấp **Next** để chuyển sang cấu hình **Network**.
   - Chọn **VPC** đã tạo trước đó, trong nội dung bài workshop này thì mình sẽ chọn ở 2 public subnets.
![Network](/images/2-TaskList/2.4-CreateASG/network.png)
6. Nhấp **Next**, trong **Configure Advanced Options**, chọn **Attach to a new load balancer** và thiết lập theo hình dưới.
![Add-LB](/images/2-TaskList/2.4-CreateASG/add-lb.png)
7. Nhấp **Next** để cấu hình **Configure group size and scaling**.
8. Thiết lập **Desired Capacity**, **Minimum Capacity**, và **Maximum Capacity**:
   - **Desired Capacity**: Số lượng phiên bản mà bạn muốn luôn chạy, mình chọn `1`.
   - **Minimum Capacity**: Số lượng phiên bản tối thiểu phải chạy trong nhóm, mình chọn `1`.
   - **Maximum Capacity**: Số lượng phiên bản tối đa có thể được khởi tạo, mình chọn `2` .
9. Về **Automatic scaling** mình chọn như hình dưới.
![Scaling-Policy](/images/2-TaskList/2.4-CreateASG/scaling-policy.png)
10. Các phần khác như **Add tags**, **Add notification** trong nội dung workshop chưa cần nền Next để bỏ qua
11. Xem lại cấu hình, sau đó nhấp **Create Auto Scaling group**.

### Bước 3: Cập nhật lại Target Group

1. Điều hướng đến phần **Target Group** trong bảng điều khiển **EC2**.
2. Chọn **Target Group** đã tạo ở bước trước(`Configure Advanced Options`).
3. Điều hướng đến tab **Health checks** và nhấn `Edit`
4. Nhập **Health check path** là `/actuator/health`.
5. Trong phần **Advanced health check settings** chọn **Health check port** là `8080`
6. Nhấn **Save changes**

## Kết luận

Bằng cách làm theo các bước trên, bạn đã tạo thành công **Launch Template**, **Auto Scaling Group**, và **Load Balancer** trên AWS. Cấu hình này đảm bảo rằng ứng dụng của bạn có thể tự động mở rộng và xử lý lưu lượng truy cập đến một cách hiệu quả, cải thiện tính sẵn sàng và khả năng chịu lỗi.
AWS cung cấp một bộ công cụ linh hoạt và mạnh mẽ để quản lý và mở rộng ứng dụng trên đám mây. Việc kết hợp các dịch vụ này cho phép bạn xây dựng một hạ tầng có tính sẵn sàng cao, khả năng chịu lỗi tốt và chi phí hợp lý.
