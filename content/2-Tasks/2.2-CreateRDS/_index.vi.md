---
title : "Tạo database RDS MySQL"
date :  "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

## Giới thiệu

Amazon Relational Database Service (Amazon RDS) là dịch vụ cơ sở dữ liệu quan hệ được quản lý hoàn toàn trên AWS, giúp bạn dễ dàng thiết lập, vận hành, và mở rộng cơ sở dữ liệu quan hệ trên đám mây. Bài hướng dẫn này sẽ giúp bạn tạo một cơ sở dữ liệu MySQL với Amazon RDS.

## Mục tiêu

Trong hướng dẫn này, chúng ta sẽ thực hiện các bước sau:
1. Tạo RDS Instance với MySQL engine.
2. Cấu hình thông số cho RDS.
3. Kiểm Tra và Tạo RDS Instance

## Các bước thực hiện

### Bước 1: Tạo RDS Instance với MySQL Engine

1. Đăng nhập vào AWS Management Console.
2. Điều hướng đến **RDS Dashboard** bằng cách tìm `RDS` trong thanh tìm kiếm.
3. Nhấp vào **Create database**.
4. Trong phần **Choose a database creation method**, chọn **Standard create**.
5. Trong phần **Engine options**, chọn **MySQL**.
![Engine](/images/2-TaskList/2.2-CreateRDS/engine.png)
6. Trong **Version**, chọn phiên bản MySQL mà bạn muốn sử dụng (ví dụ: `MySQL 8.0.35`).
7. Trong phần **Templates**, chọn **Free tier** để sử dụng phiên bản miễn phí (hoặc chọn các tùy chọn khác nếu cần).
![Tier](/images/2-TaskList/2.2-CreateRDS/free-tier.png)
8. Điền các thông tin cấu hình cơ bản:
   - **DB instance identifier**: Tên của RDS instance, ví dụ: `my-mysql-instance`.
   - **Master username**: Tên người dùng chính, ví dụ: `admin`.
   - **Master password** và **Confirm password**: Mật khẩu cho người dùng chính.

### Bước 2: Cấu Hình Thông Số Cho RDS Instance

1. Trong phần **DB instance size**, chọn loại **Burstable classes (includes t classes)** để tiết kiệm chi phí.
2. Trong phần **Storage**, bạn có thể giữ nguyên **General Purpose (SSD)** với dung lượng mặc định là `20 GiB`.
3. Trong phần **Connectivity**:
![VPC](/images/2-TaskList/2.2-CreateRDS/vpc.png)
   - **Virtual Private Cloud (VPC)**: Chọn VPC mà bạn đã tạo ở bước trước.
   - **Subnet group**: Chọn Subnet Group phù hợp với VPC của bạn.
   - **Public access**: Chọn `No`, (Chỉ chọn nếu bạn muốn truy cập công khai vào cơ sở dữ liệu, không khuyến nghị cho môi trường production).
   - **VPC security group (firewall)**: Tạo nhóm bảo mật mới hoặc chọn nhóm bảo mật hiện có để cho phép truy cập từ địa chỉ IP hoặc các dịch vụ cụ thể.
4. Trong phần **Additional configuration**:
![Database](/images/2-TaskList/2.2-CreateRDS/database_name.png)
   - **Initial database name**: Nhập tên cơ sở dữ liệu ban đầu, ví dụ: `mydatabase`.
   - **Backup retention period**: Chọn số ngày muốn giữ lại bản sao lưu (ví dụ: `7`).
   - **Enable Enhanced monitoring**: Chọn `No` hoặc `Yes` dựa trên nhu cầu giám sát của bạn.

### Bước 3: Kiểm Tra và Tạo RDS Instance

1. Kiểm tra lại các cấu hình đã chọn.
2. Nhấp vào **Create database** để tạo RDS MySQL instance.
3. Chờ quá trình tạo hoàn tất (có thể mất vài phút). Bạn có thể kiểm tra trạng thái trong **Databases** tab của RDS Dashboard.

### Bước 4: Thêm thiết lập Multi-AZ cho Database
1. Sau khi đã tạo xong database, ra ngoài màn hình danh sách database, chọn vào database đã tạo.
2. Chọn **Actions**, rồi chọn **Convert to Multi-AZ deployment**
![Multi-AZ](/images/2-TaskList/2.2-CreateRDS/multi-az.png)

1. Ở bước này, có thể chọn giữa **Apply during the next scheduled maintenance window**(Áp dụng trong thời gian bảo trì theo lịch trình tiếp theo)  hoặc **Apply immediately** (Áp dụng ngay lập tức). Mình thì chọn **Apply immediately**
![Convert](/images/2-TaskList/2.2-CreateRDS/convert.png)

4. Xác nhận hoàn tất bằng cách xem thông tin Multi-AZ đã chuyển sang Yes chưa.
![Success](/images/2-TaskList/2.2-CreateRDS/convert-success.png)

## Kết luận
Bạn đã hoàn thành việc tạo một Database MySQL cơ bản với tùy chọn Multi-AZ đảm bảo tính sẵn sàng. Database này có thể tùy chỉnh thêm để phù hợp với các yêu cầu của ứng dụng.
