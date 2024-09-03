---
title: "Clean up"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4. </b> "
---
## Introduction

After completing the workshop, it is essential to clean up the AWS resources created to avoid any unwanted charges. This guide will help you safely delete resources such as S3 Buckets, CodeBuild Projects, CodePipeline, CodeDeploy Applications, and EC2 Instances.

## Steps to Clean Up Resources

### 1. Delete CodePipeline

1. Navigate to **CodePipeline** in the **AWS Management Console**.
2. Select the pipeline you created for the Frontend and Backend applications.
3. Click **Delete** and confirm the action.

### 2. Delete CodeBuild Project

1. Navigate to **CodeBuild** in the **AWS Management Console**.
2. Select the project created for Frontend and Backend.
3. Click **Delete** and confirm the action.

### 3. Delete CodeDeploy Application

1. Navigate to **CodeDeploy** in the **AWS Management Console**.
2. Select the application created for the Backend.
3. Click **Delete** and confirm the action.

### 4. Delete EC2 Instances and Auto Scaling Group

1. Navigate to **EC2** in the **AWS Management Console**.
2. Select the EC2 instances used to deploy the Backend application.
3. Click **Actions > Instance State > Terminate** and confirm the action.
4. Navigate to **Auto Scaling Groups**.
5. Select the groups created and click **Delete**.

### 5. Delete Load Balancer

1. Navigate to **EC2 > Load Balancers** in the **AWS Management Console**.
2. Select the **Load Balancer** created for the Backend.
3. Click **Delete** and confirm the action.

### 6. Delete S3 Bucket

1. Navigate to **S3** in the **AWS Management Console**.
2. Select the S3 bucket created for the Frontend application.
3. Click **Empty** to delete the objects inside the bucket.
4. Once the bucket is empty, click **Delete bucket** and confirm the bucket name to delete it.

### 7. Delete IAM Roles and Policies

1. Navigate to **IAM** in the **AWS Management Console**.
2. Select **Roles** and delete the roles created for CodeBuild, CodeDeploy, and other services.
3. Select **Policies** and delete any custom policies created.

### 8. Delete RDS Instance

1. Navigate to **RDS** in the **AWS Management Console**.
2. Select **Databases** and choose the RDS instance you created.
3. Click **Delete**.
4. Uncheck **Create final snapshot** to avoid creating a final backup (or choose to create one if you prefer).
5. Select **Delete automated backups** to remove the automated backups.
6. Enter **delete me** in the confirmation box and click **Delete**.

### 9. Delete VPC

1. Navigate to **VPC** in the **AWS Management Console**.
2. Select **Your VPCs** and choose the VPC you created.
3. Delete all **Subnets**, **Route Tables**, **Internet Gateways**, and **NAT Gateways** associated with the VPC.
4. Once all related resources are deleted, select the VPC and click **Delete VPC**.
