---
title : "Tạo các file cấu hình YML cho Frontend và Backend"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : "<b>2.3.</b>"
---

## Giới thiệu

AWS CodeBuild và CodeDeploy là hai dịch vụ quan trọng trong bộ công cụ CI/CD của AWS giúp bạn tự động hóa quy trình xây dựng và triển khai ứng dụng. Sử dụng kết hợp hai dịch vụ này giúp bạn tăng tốc độ phát triển, cải thiện chất lượng mã nguồn và giảm thiểu rủi ro khi triển khai.

- **AWS CodeBuild** là dịch vụ xây dựng liên tục (Continuous Integration - CI) có khả năng mở rộng và được quản lý hoàn toàn, có thể biên dịch mã nguồn, chạy các bài kiểm tra và tạo các gói triển khai sẵn sàng.
- **AWS CodeDeploy** là dịch vụ triển khai liên tục (Continuous Deployment - CD) tự động hóa việc triển khai mã nguồn vào các phiên bản Amazon EC2, AWS Fargate, AWS Lambda và cả các server tại chỗ.

## Mục tiêu
Trong hướng dẫn này, chúng ta sẽ thực hiện các bước sau:
1. Tạo S3 bucket để host website cho Frontend.
2. Thiết lập file cấu hình CodeBuild cho Frontend.
3. Thêm các tham số cần thiết ở bước build phía Backend vào Parameter Store.
4. Thiết lập file cấu hình CodeBuild cho Backend.
5. Thiết lập file cấu hình CodeDeploy cho Backend.

## Các bước thực hiện:

### Bước 1: Tạo S3 bucket để host website cho Frontend.
1. Tạo S3 Bucket mới:
   - Đăng nhập vào **AWS Management Console**.
   - Điều hướng đến **Amazon S3** bằng cách tìm "S3" trong thanh tìm kiếm.
   - Nhấp vào **Create bucket**.
![Create Bucket](/images/2-TaskList/2.3-CreateYamlFile/create-bucket.png)
   - Cấu hình các thông số cho bucket:
     - **Bucket name**: Nhập tên duy nhất cho bucket của bạn (ví dụ: `my-frontend-website-bucket`).
     - **Region**: Chọn khu vực (Region) phù hợp (ví dụ: `us-east-1`).
   - Các tùy chọn khác có thể để mặc định.
   - Bỏ chọn **Block all public access**.
   - Chọn **I acknowledge that the current settings might result in this bucket and the objects within becoming public**.
   - Nhấp vào **Create bucket**.

2. Cấu hình S3 bucket để host website tĩnh :
   - Chọn bucket vừa tạo.
   - Điều hướng đến tab **Properties**.
   - Cuộn xuống và nhấp vào **Edit** trong phần **Static website hosting**.
   - Chọn **Enable**.
   - Trong phần **Index document**, nhập tên của tệp trang chính của bạn, ví dụ: `index.html`.
   - Trong phần **Error document**, bạn có thể nhập `error.html` hoặc để trống nếu không có trang lỗi tùy chỉnh.
   - Nhấp **Save changes**.


3. Thêm chính sách bucket để cho phép truy cập công khai:
   - Điều hướng đến tab **Permissions**.
   - Cuộn xuống phần **Bucket policy** và nhấp vào **Edit**.
   - Thêm chính sách JSON sau:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::my-frontend-website-bucket/*"
       }
     ]
   }
   ```
   - Thay thế `my-frontend-website-bucket` bằng tên bucket của bạn.
   - Nhấp **Save changes**.

### Bước 2: Thiết lập file cấu hình CodeBuild cho Frontend.
  - Trong thư mục root của folder dự án thêm 1 file có tên là `buildspec.yml`:
```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      nodejs: 20
    commands:
      - echo "Installing dependencies..."
      - npm install
  build:
    commands:
      - echo "Building React project..."
      - npm run build
  post_build:
    commands:
      - echo "Updating files in the S3 bucket..."
      - aws s3 sync build/ s3://my-frontend-website-bucket --delete
```
  - Thay thế `my-frontend-website-bucket` bằng tên bucket của bạn.
  - Bằng file cấu hình trên khi **CodeBuild** chạy sẽ chọn đúng môi trường **nodejs** và thực hiện các bước build project, cuối cùng sẽ tải lên các file được build vào bucket frontend của bạn.

### Bước 3: Thêm các tham số cần thiết ở bước build phía Backend vào Parameter Store.
   - Đăng nhập vào **AWS Management Console**.
   - Điều hướng đến **Parameter Store** bằng cách tìm `Parameter Store` trong thanh tìm kiếm.
   - Nhấp vào **Create parameter**.
![Create-Parameter](/images/2-TaskList/2.3-CreateYamlFile/create-parameter.png)
   - Điền **Name** là **DB_URL**
   - Điền **Value** là `jdbc:mysql://my-mysql-instance.c3e6oeawqqnk.us-east-1.rds.amazonaws.com:3306/employee_management`
   - Trong đó `my-mysql-instance.c3e6oeawqqnk.us-east-1.rds.amazonaws.com`,  `employee_management` lần lượt là **endpoint** và **Initial database name** của RDS instance đã tạo ở bước nhiệm vụ trước.
   - Tương tự, tạo các tham số **DB_USER**, **DB_USER** với giá trị **Master username** và **Master password** của RDS instance đã tạo ở bước nhiệm vụ trước.

### Bước 4: Thiết lập file cấu hình CodeBuild cho Backend.
  - Trong thư mục root của folder dự án, thêm 1 file có tên là `buildspec.yml`:
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto17
    commands:
      - echo "Installing AWS CLI..."
      - sudo yum install -y awscli
  pre_build:
    commands:
      - echo "Retrieving parameters from SSM..."
      - export DB_URL=$(aws ssm get-parameter --name "DB_URL" --with-decryption --query "Parameter.Value" --output text)
      - export DB_USER=$(aws ssm get-parameter --name "DB_USER" --with-decryption --query "Parameter.Value" --output text)
      - export DB_PASSWORD=$(aws ssm get-parameter --name "DB_PASSWORD" --with-decryption --query "Parameter.Value" --output text)
      - echo "spring.datasource.url=${DB_URL}" > src/main/resources/application.properties
      - echo "spring.datasource.username=${DB_USER}" >> src/main/resources/application.properties
      - echo "spring.datasource.password=${DB_PASSWORD}" >> src/main/resources/application.properties
      - echo "spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver" >> src/main/resources/application.properties
      - echo "spring.jpa.hibernate.ddl-auto=update" >> src/main/resources/application.properties
      - echo "management.endpoints.web.exposure.include=health" >> src/main/resources/application.properties

  build:
    commands:
      - echo "Building the application..."
      - mvn clean package -DskipTests
artifacts:
  files:
    - appspec.yml
    - scripts/**/*
    - target/*.jar
  discard-paths: no
```
  - Bằng file cấu hình trên khi **CodeBuild** chạy sẽ thực hiện lần lượt các bước sau:
    - Chọn môi trường Java 17 và cài đặt AWS CLI.
    - Lấy các tham số từ **Parameter Store** và thêm vào file `application.properties`.
    - Build project ra file `jar`.
    - Lưu lại các file `target/*.jar`, `appspec.yml`, `scripts` để sử dụng trong bước deploy.

### Bước 5: Thiết lập file cấu hình CodeDeploy cho Frontend.
  - Trong thư mục root của folder dự án, thêm 1 file có tên là `appspec.yml`:
```yaml
version: 0.0
os: linux
files:
  - source: target/
    destination: /home/ec2-user/app
hooks:
  AfterInstall:
    - location: /scripts/deploy.sh
      timeout: 300
```
  - Trong thư mục root của folder dự án, tạo thư mục `scripts`, thêm file có tên là `deploy.sh` vào thư mục `scripts` với nội dung như sau:
```sh
#!/bin/bash

# Tìm file JAR mới nhất trong thư mục đích
JAR_FILE=$(ls -t /home/ec2-user/app/*.jar | head -n 1)

# Dừng ứng dụng hiện tại nếu có
sudo pkill -f 'java -jar' || true

# Khởi động ứng dụng mới từ file JAR đã tìm được ở chế độ nền
#!/bin/bash

# Tìm file JAR mới nhất trong thư mục đích
JAR_FILE=$(ls -t /home/ec2-user/app/*.jar | head -n 1)

# Dừng ứng dụng hiện tại nếu có
sudo pkill -f 'java -jar' || true

# Khởi động ứng dụng mới từ file JAR đã tìm được ở chế độ nền
nohup java -jar "$JAR_FILE" > /dev/null 2>&1 &

# In ra thông báo thành công
echo "Ứng dụng đã được khởi động lại thành công."
```
  - Bằng các file cấu hình trên khi **CodeDeploy** chạy sẽ thực hiện các bước sau:
    - Lấy file `.jar` từ bước build và đưa vào thư mục `/home/ec2-user/app`.
    - Sau khi đã lấy được các file thì từ bước build thì sẽ deploy theo file `scripts/deploy.sh` .
