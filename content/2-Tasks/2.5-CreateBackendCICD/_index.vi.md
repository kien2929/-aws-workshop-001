---
title : "Tạo CodePipeline, CodeBuild, CodeDeploy cho CICD Backend"
date :  "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.5. </b> "
---
## Giới thiệu

AWS cung cấp một bộ công cụ dành cho các nhà phát triển giúp tự động hóa các quy trình xây dựng, triển khai và quản lý ứng dụng. Trong số các công cụ này, **AWS CodeBuild**, **AWS CodeDeploy**, và **AWS CodePipeline** đóng vai trò quan trọng trong việc thiết lập quy trình Tích hợp liên tục và Triển khai liên tục (CI/CD).

- **AWS CodeBuild**: Là một dịch vụ xây dựng được quản lý hoàn toàn, có khả năng biên dịch mã nguồn, chạy thử nghiệm và tạo các gói phần mềm sẵn sàng để triển khai. CodeBuild loại bỏ nhu cầu cung cấp, quản lý và mở rộng máy chủ xây dựng của riêng bạn.

- **AWS CodeDeploy**: Là một dịch vụ tự động hóa việc triển khai mã đến bất kỳ máy chủ nào, bao gồm các phiên bản Amazon EC2 và các máy chủ tại chỗ. Nó đơn giản hóa quá trình triển khai và giúp duy trì tính khả dụng cao.

- **AWS CodePipeline**: Là một dịch vụ triển khai liên tục được quản lý hoàn toàn, giúp tự động hóa các giai đoạn xây dựng, thử nghiệm và triển khai trong quy trình phát hành của bạn. Nó tích hợp với CodeBuild, CodeDeploy và các dịch vụ AWS khác để mang lại trải nghiệm CI/CD liền mạch.

## Mục tiêu

Hướng dẫn này sẽ giúp bạn thực hiện các bước sau:

1. Tạo một dự án CodeBuild cho ứng dụng Backend.
2. Thiết lập CodeDeploy để triển khai ứng dụng đến các phiên bản EC2.
3. Tạo CodePipeline để điều phối quy trình CICD.

## Các bước thiết lập CodeBuild, CodeDeploy, và CodePipeline

### Bước 1: Tạo một dự án CodeBuild cho ứng dụng Backend

1. Đăng nhập vào **AWS Management Console**.
2. Điều hướng đến **CodeBuild** bằng cách tìm `CodeBuild` trong thanh tìm kiếm.
3. Nhấp vào **Create build project**.
4. Nhập **Tên dự án (Project name)**.
5. Trong phần **Source**:
   - Chọn **Nhà cung cấp nguồn (Source provider)** ở đây mình chọn `GitHub`.
   - Kết nối với kho nguồn ứng dụng backend của bạn.
   - Source version: ở đây mình chọn điền `main` nhé
![CodeBuild-Name](/images/2-TaskList/2.5-CreateBackendCICD/codebuild-name.png)
6. Trong phần **Primary source webhook events**:
   - Tích vào **Rebuild every time a code change is pushed to this repository**
   - **Filter group** chọn **Event type** là `PUSH`: ở đây mình chọn sự kiện push thêm code vào kho nguồn, các bạn có thể thêm các sự kiện khác nữa nếu cần.
![Event-Type](/images/2-TaskList/2.5-CreateBackendCICD/event-type.png)
7. Trong phần **Environment**:
   - Đối với **Hệ điều hành (Operating system)**, chọn `Amazon Linux`.
   - Đối với **Runtime**, chọn `Standard`.
   - Chọn **New service role** để cho phép CodeBuild tạo một vai trò mới cho bạn.
![Env-Role](/images/2-TaskList/2.5-CreateBackendCICD/env-role.png)
8. Trong phần **Buildspec**:
   - Chọn **Use a buildspec file**(tệp `buildspec.yml` trong kho đã tạo ở bước trước).
9. Nhấp vào **Create build project**.
10. Cập nhật Permission cho Service Role vừa tạo ở trên:
   - Click vào link Service Role vừa tạo ở trên.
![Link-Role](/images/2-TaskList/2.5-CreateBackendCICD/link-role.png)
   - Chọn **Add Permissions** và chọn **Create inline policy**
![Inline-Policy](/images/2-TaskList/2.5-CreateBackendCICD/inline-policy.png)
   - Thêm policy sau dưới sạng Json (thay `010928203475` thành Account ID của bạn):
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:GetParameter",
                "ssm:GetParameters"
            ],
            "Resource": [
                "arn:aws:ssm:us-east-1:010928203475:parameter/DB_URL",
                "arn:aws:ssm:us-east-1:010928203475:parameter/DB_USER",
                "arn:aws:ssm:us-east-1:010928203475:parameter/DB_PASSWORD"
            ]
        }
    ]
}
```
   - Nhấp vào **Next**, điền thông tin **Policy Name** rồi chọn **Create policy**.
![Create-Policy](/images/2-TaskList/2.5-CreateBackendCICD/create-policy.png)

### Bước 2: Thiết lập CodeDeploy để triển khai ứng dụng đến các phiên bản EC2
1. Điều hướng đến **CodeDeploy** trong **AWS Management Console**.
2. Nhấp vào **Applications** và chọn **Create application**.
3. Nhập **Tên ứng dụng (Application name)** và chọn một **Compute platform** : `EC2/On-premises`.
4. Nhấp vào **Create application**.
![Create-App](/images/2-TaskList/2.5-CreateBackendCICD/create-application.png)
5. Trước khi bắt đầu tạo **deployment group** phải tạo Role cho CodeDeploy:
   - Vào màn hình **IAM Dashboard**, chọn **Roles**, nhấp vào **Create role**.
   - Chọn **AWS Service**, sau đó chọn **CodeDeploy**.
   - Đảm bảo **Permission policies** bao gồm **AWSCodeDeployRole**
   - Đặt tên cho vai trò mới (ví dụ: **CodeDeployServiceRole**) rồi chọn **Create role**.
6. Trở lại CodeDeploy Application, chọn **Create deployment group**.
7. Nhập **Deployment group name**.
8. Chọn **Service role** mà ta vừa tạo ở trên.
9. Trong **Deployment type**, chọn `In-place` hoặc `Blue/Green`.
![Deployment-Group](/images/2-TaskList/2.5-CreateBackendCICD/deployment-group.png)
10. Trong **Environment configuration**, chọn **Auto Scaling groups** đã tạo trước đấy.
11. Trong **Deployment settings**, chọn `CodeDeployDefault.OneAtATime`.
![Deployment-Setting](/images/2-TaskList/2.5-CreateBackendCICD/deployment-settings.png)
12. Nhấp vào **Create deployment group**.

### Bước 3: Tạo CodePipeline để điều phối quy trình CICD
1. Điều hướng đến **CodePipeline** trong **AWS Management Console**.
2. Nhấp vào **Create pipeline**.
3. Nhập **Tên pipeline (Pipeline name)** và chọn **New service role** trong **Service role**.
![codepipeline](/images/2-TaskList/2.5-CreateBackendCICD/codepipeline.png)
4. Trong **Source stage**:
   - Chọn **Source provider**: `GitHub`.
   - Chọn **Kho (Repository)** và **Nhánh (Branch)** mà bạn muốn sử dụng.
   - Nhấp **Next**.
![source](/images/2-TaskList/2.5-CreateBackendCICD/git-source.png)
5. Trong **Build stage**:
   - Chọn **AWS CodeBuild** là **Nhà cung cấp xây dựng (Build provider)**.
   - Chọn **CodeBuild project** đã tạo ở Bước 1.
   - Nhấp **Next**.
![build-step](/images/2-TaskList/2.5-CreateBackendCICD/build-step.png)
6. Trong **Deploy stage**:
   - Chọn **AWS CodeDeploy** là **Nhà cung cấp triển khai (Deploy provider)**.
   - Chọn **Application name** và **Nhóm triển khai (Deployment group)** đã tạo ở Bước 2.
   - Nhấp **Next**.
![deploy-step](/images/2-TaskList/2.5-CreateBackendCICD/deploy-step.png)
7. Xem lại cài đặt của pipeline và nhấp **Create pipeline**.

## Kết luận

Bằng cách làm theo các bước trên, bạn đã thiết lập thành công **CodeBuild**, **CodeDeploy**, và **CodePipeline** trên AWS. Cấu hình này cho phép bạn thiết lập một pipeline CI/CD mạnh mẽ, tự động hóa các quy trình xây dựng, thử nghiệm và triển khai, từ đó nâng cao hiệu suất phát triển và giảm thiểu lỗi khi triển khai.

AWS cung cấp một bộ công cụ mạnh mẽ để xây dựng, triển khai và quản lý ứng dụng trên đám mây. Việc kết hợp các dịch vụ này cho phép bạn tạo ra một pipeline CI/CD tự động và mở rộng, đảm bảo quy trình phát hành phần mềm nhanh chóng và đáng tin cậy.
