---
title : "Dọn dẹp"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
## Giới thiệu

Sau khi hoàn thành workshop, việc dọn dẹp các tài nguyên AWS đã tạo là cần thiết để tránh các chi phí phát sinh không mong muốn. Hướng dẫn này sẽ giúp bạn xóa các tài nguyên như S3 Bucket, CodeBuild Project, CodePipeline, CodeDeploy Application, và EC2 Instances một cách an toàn.

## Các bước dọn dẹp tài nguyên

### 1. Xóa CodePipeline

1. Điều hướng đến **CodePipeline** trong **AWS Management Console**.
2. Chọn pipeline mà bạn đã tạo cho ứng dụng Frontend và Backend.
3. Nhấp vào **Delete** và xác nhận hành động.

### 2. Xóa CodeBuild Project

1. Điều hướng đến **CodeBuild** trong **AWS Management Console**.
2. Chọn project đã tạo cho Frontend và Backend.
3. Nhấp vào **Delete** và xác nhận hành động.

### 3. Xóa CodeDeploy Application

1. Điều hướng đến **CodeDeploy** trong **AWS Management Console**.
2. Chọn application đã tạo cho Backend.
3. Nhấp vào **Delete** và xác nhận hành động.

### 4. Xóa EC2 Instances và Auto Scaling Group

1. Điều hướng đến **EC2** trong **AWS Management Console**.
2. Chọn các EC2 instances đã sử dụng để triển khai ứng dụng Backend.
3. Nhấp vào **Actions > Instance State > Terminate** và xác nhận hành động.
4. Điều hướng đến **Auto Scaling Groups**.
5. Chọn các nhóm đã tạo và nhấp vào **Delete**.

### 5. Xóa Load Balancer

1. Điều hướng đến **EC2 > Load Balancers** trong **AWS Management Console**.
2. Chọn **Load Balancer** đã tạo cho Backend.
3. Nhấp vào **Delete** và xác nhận hành động.

### 6. Xóa S3 Bucket

1. Điều hướng đến **S3** trong **AWS Management Console**.
2. Chọn S3 bucket đã tạo cho ứng dụng Frontend.
3. Nhấp vào **Empty** để xóa các đối tượng bên trong bucket.
4. Sau khi bucket trống, nhấp vào **Delete bucket** và xác nhận tên bucket để xóa.

### 7. Xóa IAM Roles và Policies

1. Điều hướng đến **IAM** trong **AWS Management Console**.
2. Chọn **Roles** và xóa các vai trò đã tạo cho CodeBuild, CodeDeploy, và các dịch vụ khác.
3. Chọn **Policies** và xóa các chính sách tùy chỉnh đã tạo.
