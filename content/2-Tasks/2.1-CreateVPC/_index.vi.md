---
title : "Tạo VPC"
date :  "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---

## Giới thiệu

Virtual Private Cloud (VPC) là một dịch vụ của Amazon Web Services (AWS) cho phép bạn khởi tạo một mạng ảo dành riêng trong AWS. Bạn có thể kiểm soát hoàn toàn môi trường mạng ảo này, bao gồm việc lựa chọn dải IP, tạo các subnets, và cấu hình các route table cũng như gateway. Bài hướng dẫn này sẽ giúp bạn tạo một VPC cơ bản trên AWS.


## Các bước thực hiện

1. Đăng nhập vào AWS Management Console.
2. Chuyển đến **VPC Dashboard** bằng cách tìm `VPC` trong thanh tìm kiếm.
3. Nhấp vào **Create VPC**.
![DashBoard](/images/2-TaskList/2.1-CreateVPC/dashboard-1.png)
4. Chọn **VPC and more** trong **VPC settings**.
5. Điền các thông tin cần thiết:
   - **Name tag**: Tên của VPC, ví dụ: `MyVPC`.
   - **IPv4 CIDR block**: Nhập dải CIDR cho VPC, ví dụ: `10.0.0.0/16`.
   - **Tenancy**: Chọn `Default`.
   - **Number of Availability Zones**, **Number of public subnets**, ... để mặc định, trong nội dung workshop này mình chọn các cấu hình trên
![Option](/images/2-TaskList/2.1-CreateVPC/option.png)
6. Nhấp vào **Create VPC**.


## Kết luận

Bạn đã hoàn thành việc tạo một VPC cơ bản với subnet công khai và riêng tư trên AWS. VPC này có thể mở rộng và tùy chỉnh thêm để phù hợp với các yêu cầu hạ tầng mạng của bạn.
