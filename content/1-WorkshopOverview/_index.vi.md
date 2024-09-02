---
title : "Tổng quan Workshop"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 1. </b> "
---
## Mục tiêu

Sau khi hoàn thành workshop này, bạn sẽ có thể:
- Tạo VPC, Auto Scaling Group, Load Balancer
- Tạo RDS đảm bảo tính sẵn sàng
- Tạo luồng CICD đầy đủ với CodePipeline, CodeBuild, CodeDeploy

## Thời lượng
Workshop này sẽ kéo dài khoảng 2 tiếng

## Kiến trúc dự án

Ứng dụng Quản lý nhân viên bao gồm 2 phần, Backend và Fronntend. Phía Backend sẽ sử dụng database Mysql RDS để lưu trữ dữ liệu. Database sẽ được đặt trong private zone và chạy multi-az với 1 az khác cũng nằm trong VPC. Server ứng dụng được thiết lập trong 1 nhóm Auto Scaling Group và nằm phía sau Load Balancer. Phía Frontend sẽ sử dụng Load Balancer để chuyển tiếp request đến các server ứng dụng.

![Diagram](/images/1-WorkshopOverview/Workshop-diagram.drawio.png)

### CICD ở phía Backend

Mã nguồn ứng dụng Backend sẽ được lưu trữ trên Github. Thiết lập 1 trigger khi có hành động PUSH ở nhánh main thì sẽ chạy CICD cho ứng dụng. Vì ứng dụng được viết bằng ngôn ngữ JAVA nên ở bước **CodeBuild** sẽ kết hợp với các biến môi trường đươc lưu trong **Parameter Store** để build ứng dụng ra file JAR. Ơ bước CodeDeploy sẽ sử dụng file JAR để deploy ứng dụng lên server. CodeDeploy sẽ đóng vai trò dừng và khởi động tại ứng dụng.

![Backend-CICD](/images/1-WorkshopOverview/Backend-CICD.drawio.png)

### CICD ở phía Frontend

Mã nguồn ứng dụng Frontend sẽ được lưu trữ trên Github. Thiết lập 1 trigger khi có hành động PUSH ở nhánh main thì sẽ chạy CICD cho ứng dụng. Vì ứng dụng được xây dựng trên framework ReactJS nên ở bước **CodeBuild** build ứng dụng ra các file static file như HTML, CSS, JavaScript sau đấy đẩy vào **S3** bucket để chạy như là 1 website.

![Frontend-CICD](/images/1-WorkshopOverview/Frontend-CICD.drawio.png)
