---
title: "Create a VPC"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1. </b> "
---

## Introduction

Virtual Private Cloud (VPC) is a service provided by Amazon Web Services (AWS) that allows you to create a dedicated virtual network within AWS. You have complete control over this virtual networking environment, including selecting IP address ranges, creating subnets, and configuring route tables and gateways. This guide will help you create a basic VPC on AWS.

## Steps to Follow

1. Log in to the AWS Management Console.
2. Go to the **VPC Dashboard** by searching for `VPC` in the search bar.
3. Click on **Create VPC**.
![DashBoard](/images/2-TaskList/2.1-CreateVPC/dashboard-1.png)
4. Select **VPC and more** in the **VPC settings**.
5. Fill in the required information:
   - **Name tag**: Name of the VPC, e.g., `MyVPC`.
   - **IPv4 CIDR block**: Enter the CIDR block for the VPC, e.g., `10.0.0.0/16`.
   - **Tenancy**: Choose `Default`.
   - Keep the **Number of Availability Zones**, **Number of public subnets**, etc., at their default settings; for this workshop, we select these configurations.
![Option](/images/2-TaskList/2.1-CreateVPC/option.png)
6. Click on **Create VPC**.

## Conclusion

You have successfully created a basic VPC with public and private subnets on AWS. This VPC can be expanded and further customized to meet your network infrastructure requirements.
