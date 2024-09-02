---
title: "Building a Scalable and Highly Available Web Application"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Building a Scalable and Highly Available Web Application

## Overview

This application will build a web application divided into frontend and backend.
1. The frontend is automatically CICD from a Github repository, built, and deployed to AWS S3.
2. The backend is automatically CICD from a Github repository, built, and deployed to AWS EC2.
3. Ensure backend scalability using an Application Load Balancer and Auto Scaling Group.
4. The database uses Multi-AZ to ensure high availability.

### Workshop Goals

In this workshop, you will practice the following:
- Create a VPC to isolate resources.
- Create an RDS database with the multi-AZ option.
- Create an Auto Scaling Group and Load Balancer for the application.
- Implement CICD with Github, CodeBuild, CodeDeploy, S3, and EC2.
