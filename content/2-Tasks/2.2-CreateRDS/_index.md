---
title : "Create an RDS MySQL database"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---

## Introduction

Amazon Relational Database Service (Amazon RDS) is a fully managed relational database service on AWS that makes it easy to set up, operate, and scale a relational database in the cloud. This guide will help you create a MySQL database using Amazon RDS.

## Objectives

In this guide, we will perform the following steps:
1. Create an RDS Instance with the MySQL engine.
2. Configure the RDS parameters.
3. Review and create the RDS Instance.
4. Add Multi-AZ Setup for the Database.

## Steps to Follow

### Step 1: Create an RDS Instance with MySQL Engine

1. Log in to the AWS Management Console.
2. Navigate to the **RDS Dashboard** by searching for `RDS` in the search bar.
3. Click on **Create database**.
4. In the **Choose a database creation method** section, select **Standard create**.
5. In the **Engine options** section, choose **MySQL**.
![Engine](/images/2-TaskList/2.2-CreateRDS/engine.png)
6. In **Version**, select the MySQL version you want to use (e.g., `MySQL 8.0.35`).
7. In the **Templates** section, choose **Free tier** to use the free version (or select other options if needed).
![Tier](/images/2-TaskList/2.2-CreateRDS/free-tier.png)
8. Fill in the basic configuration details:
   - **DB instance identifier**: Name of the RDS instance, e.g., `my-mysql-instance`.
   - **Master username**: The main user name, e.g., `admin`.
   - **Master password** and **Confirm password**: Password for the main user.

### Step 2: Configure RDS Instance Parameters

1. In the **DB instance size** section, choose **Burstable classes (includes t classes)** to save costs.
2. In the **Storage** section, you can keep **General Purpose (SSD)** with the default size of `20 GiB`.
3. In the **Connectivity** section:
![VPC](/images/2-TaskList/2.2-CreateRDS/vpc.png)
   - **Virtual Private Cloud (VPC)**: Select the VPC you created in the previous step.
   - **Subnet group**: Select the Subnet Group that matches your VPC.
   - **Public access**: Select `No` (Only choose this if you want public access to the database, which is not recommended for production environments).
   - **VPC security group (firewall)**: Create a new security group or choose an existing one to allow access from specific IP addresses or services.
4. In the **Additional configuration** section:
![Database](/images/2-TaskList/2.2-CreateRDS/database_name.png)
   - **Initial database name**: Enter the initial database name, e.g., `mydatabase`.
   - **Backup retention period**: Choose the number of days to retain backups (e.g., `7`).
   - **Enable Enhanced monitoring**: Select `No` or `Yes` based on your monitoring needs.

### Step 3: Review and Create RDS Instance

1. Review the configurations you have selected.
2. Click on **Create database** to create the RDS MySQL instance.
3. Wait for the creation process to complete (this may take a few minutes). You can check the status in the **Databases** tab of the RDS Dashboard.

### Step 4: Add Multi-AZ Setup for the Database

1. After creating the database, go to the database list screen and select the created database.
2. Choose **Actions**, then select **Convert to Multi-AZ deployment**.
![Multi-AZ](/images/2-TaskList/2.2-CreateRDS/multi-az.png)

3. In this step, you can choose between **Apply during the next scheduled maintenance window** or **Apply immediately**. For this workshop, select **Apply immediately**.
![Convert](/images/2-TaskList/2.2-CreateRDS/convert.png)

4. Confirm completion by checking if the Multi-AZ information has changed to Yes.
![Success](/images/2-TaskList/2.2-CreateRDS/convert-success.png)

## Conclusion

You have successfully created a basic MySQL Database with a Multi-AZ option to ensure high availability. This database can be further customized to meet the requirements of your application.
