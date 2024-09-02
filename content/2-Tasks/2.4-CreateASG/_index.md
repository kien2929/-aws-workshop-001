---
title : "Create Launch Template, Auto Scaling Group, and Load Balancer"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : "<b>2.4.</b>"
---
## Introduction

In AWS, managing scalability, availability, and efficiency of applications involves using various services. Notably among these are **Launch Templates**, **Auto Scaling Groups (ASGs)**, and **Load Balancers**. Together, they provide a powerful mechanism to deploy and manage applications in a flexible and reliable manner.

- **Launch Template**: A Launch Template is a versioned instance configuration template that you can use to launch EC2 instances. It allows you to specify the Amazon Machine Image (AMI), instance type, key pair, security groups, and other parameters.

- **Auto Scaling Group (ASG)**: An Auto Scaling Group manages a group of EC2 instances with similar characteristics. ASG ensures that your application can automatically scale up or down as needed and maintain a desired number of healthy instances.

- **Load Balancer**: A Load Balancer automatically distributes incoming application traffic across multiple targets, such as EC2 instances, containers, or IP addresses. This helps improve the availability and fault tolerance of your application.

## Objectives

This guide will help you perform the following steps:

1. Create a Launch Template to define instance configuration.
2. Set up an Auto Scaling Group and Load Balancer using the Launch Template.
3. Update the Target Group.

## Steps to Implement

### Step 1: Create a Launch Template

1. Log in to the **AWS Management Console**.
2. Navigate to **EC2** by searching for `EC2` in the search bar.
3. On the left-hand sidebar, click on **Launch Templates**.
4. Click on **Create Launch Template**.
5. Enter the **Launch Template Name** and an optional **Description**.
![Template-Name](/images/2-TaskList/2.4-CreateASG/Template-Name.png)
6. Select the **Amazon Machine Image (AMI)** as `Amazon Linux 2 AMI`.
![Instance-type](/images/2-TaskList/2.4-CreateASG/instance-type.png)
7. Choose **Instance Type** as `t2.micro` (Free tier eligible).
8. In the **Create key Pair** section, select `Create new key pair`, enter a Key pair name, and choose **Create key pair**.
![Key-Pair](/images/2-TaskList/2.4-CreateASG/key-pair.png)
9. In **Network Settings**, choose **Create security group**, select the **VPC** created in the previous step, and add 2 **Inbound Security Group Rules** as shown below.
![security-group](/images/2-TaskList/2.4-CreateASG/security-group.png)
10. In **Advanced Details**, add the following **User Data** to set up the environment and deploy an agent on the instance:
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
11. Click on **Create Launch Template**.

### Step 2: Set Up Auto Scaling Group and Load Balancer Using the Launch Template

1. Navigate to **Auto Scaling Groups** in the **EC2** dashboard.
2. Click on **Create Auto Scaling group**.
3. Enter the **Auto Scaling group name**.
4. In **Launch Template**, select the **Launch Template** created in Step 1.
5. Click **Next** to move to the **Network** configuration.
   - Select the **VPC** created earlier; for this workshop, we will choose 2 public subnets.
   ![Network](/images/2-TaskList/2.4-CreateASG/network.png)
6. Click **Next**; in **Configure Advanced Options**, select **Attach to a new load balancer** and configure it as shown below.
   ![Add-LB](/images/2-TaskList/2.4-CreateASG/add-lb.png)
7. Click **Next** to configure **Configure group size and scaling**.
8. Set **Desired Capacity**, **Minimum Capacity**, and **Maximum Capacity**:
   - **Desired Capacity**: The number of instances you want to always run; here, we choose `1`.
   - **Minimum Capacity**: The minimum number of instances that must run in the group; we choose `1`.
   - **Maximum Capacity**: The maximum number of instances that can be created; we choose `2`.
9. For **Automatic scaling**, choose as shown below.
   ![Scaling-Policy](/images/2-TaskList/2.4-CreateASG/scaling-policy.png)
10. For other sections like **Add tags** and **Add notification**, click Next to skip them as they are not needed in this workshop.
11. Review the configuration, then click **Create Auto Scaling group**.

### Step 3: Update the Target Group

1. Navigate to **Target Group** in the **EC2** dashboard.
2. Select the **Target Group** created in the previous step (`Configure Advanced Options`).
3. Go to the **Health checks** tab and click `Edit`.
4. Set the **Health check path** to `/actuator/health`.
5. In **Advanced health check settings**, choose **Health check port** as `8080`.
6. Click **Save changes**.

## Conclusion

By following the above steps, you have successfully created a **Launch Template**, **Auto Scaling Group**, and **Load Balancer** on AWS. This configuration ensures that your application can automatically scale and handle incoming traffic efficiently, enhancing availability and fault tolerance.

AWS provides a flexible and powerful set of tools to manage and scale applications on the cloud. Combining these services allows you to build a highly available, fault-tolerant, and cost-effective infrastructure.
