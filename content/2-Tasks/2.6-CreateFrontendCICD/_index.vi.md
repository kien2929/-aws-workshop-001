---
title : "Tạo S3 Bucket, CodePipeline, CodeBuild, CodeDeploy cho CICD Frontend"
date :  "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 2.6. </b> "
---
## Mục tiêu

Hướng dẫn này sẽ giúp bạn thực hiện các bước sau:

1. Tạo một dự án CodeBuild cho ứng dụng Backend.
2. Tiến hành nghiệm thu và kiểm tra ứng dụng.

## Các bước thực hiện

### Bước 1: Tạo một dự án CodeBuild cho ứng dụng Backend

1. Đăng nhập vào **AWS Management Console**.
2. Điều hướng đến **CodeBuild** bằng cách tìm `CodeBuild` trong thanh tìm kiếm.
3. Nhấp vào **Create build project**.
4. Nhập **Tên dự án (Project name)**.
5. Trong phần **Source**:
   - Chọn **Nhà cung cấp nguồn (Source provider)** ở đây mình chọn `GitHub`.
   - Kết nối với kho nguồn ứng dụng frontend của bạn.
   - Source version: ở đây mình chọn điền `main` nhé
![CodeBuild-Name](/images/2-TaskList/2.6-CreateFrontendCICD/codebuild-name.png)
6. Trong phần **Primary source webhook events**:
   - Tích vào **Rebuild every time a code change is pushed to this repository**
   - **Filter group** chọn **Event type** là `PUSH`: ở đây mình chọn sự kiện push thêm code vào kho nguồn, các bạn có thể thêm các sự kiện khác nữa nếu cần.
![Event-Type](/images/2-TaskList/2.5-CreateBackendCICD/event-type.png)
7. Trong phần **Environment**:
   - Đối với **Hệ điều hành (Operating system)**, chọn `Amazon Linux`.
   - Đối với **Runtime**, chọn `Standard`.
   - Chọn **New service role** để cho phép CodeBuild tạo một vai trò mới cho bạn.
![Env-Role](/images/2-TaskList/2.6-CreateFrontendCICD/env-role.png)
8. Trong phần **Buildspec**:
   - Chọn **Use a buildspec file**(tệp `buildspec.yml` trong kho đã tạo ở bước trước).
9. Nhấp vào **Create build project**.
10. Cập nhật Permission cho Service Role vừa tạo ở trên:
   - Click vào link Service Role vừa tạo ở trên.
   - Chọn **Add Permissions** và chọn **Create inline policy**
   - Thêm policy sau dưới sạng Json (thay `bucket-frontend` thành bucket frontend của bạn):
```json
{
    "Effect": "Allow",
    "Resource": [
        "arn:aws:s3:::bucket-frontend/*",
        "arn:aws:s3:::bucket-frontend"
    ],
    "Action": [
        "s3:PutObject",
        "s3:PutObjectAcl",
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:ListBucket"
    ]
}
```
   - Nhấp vào **Next**, điền thông tin **Policy Name** rồi chọn **Create policy**.

### Bước 2: Tiến hành nghiệm thu và kiểm tra ứng dụng
   - Lấy DNS từ Load Balancer và thêm vào `.env` của ứng dụng frontend(Chỉ trong nội dung bài workshop này):
![lb-dns](/images/2-TaskList/2.6-CreateFrontendCICD/lb-dns.png)
   - Push các cập nhật lên kho nguồn của bạn ở cả Frontend và Backend.
   - Kiểm tra CodeBuild và CodePipeline của bạn đã hoạt động chưa.
![deploy-result](/images/2-TaskList/2.6-CreateFrontendCICD/deploy-result.png)
   - Xác nhận ứng dụng đã được triển khai thành công.
![web](/images/2-TaskList/2.6-CreateFrontendCICD/web.png)
