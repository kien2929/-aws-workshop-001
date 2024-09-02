---
title : "Xây dựng Ứng dụng web có tính mở rộng và sẵn sàng cao"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
---
# Xây dựng Ứng dụng web có tính mở rộng và sẵn sàng cao

## Tổng quan

Ứng dụng này sẽ xây dựng một ứng dụng web chia ra làm frontend và backend.
1. Frontend được tự động CICD từ repository Github. build, deploy lên AWS S3
2. Backend được tự động CICD từ repository Github, build và deploy lên AWS EC2
3. Đảm bảo tính mở rộng Backend bằng cách sử dụng Application Load Balancer và Auto Scaling Group
4. Database sử dụng Multi-AZ để đảm bảo tính sẵn sàng cao

### Mục tiêu Workshop

Trong nội dung workshop này, bạn sẽ thực hành các nội dung sau:
- Tạo VPC để cô lập tài nguyên
- Tạo Database RDS với tùy chọn multi-az
- Tạo Auto Scaling Group và Load Balancer cho ứng dụng
- CICD với Github, CodeBuild, CodeDeploy, S3 và EC2
