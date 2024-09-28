---
title: "Workshop Overview"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---
## Objectives

After completing this workshop, you will be able to:
- Create a VPC, Auto Scaling Group, and Load Balancer
- Create an RDS with high availability
- Create a complete CI/CD pipeline using CodePipeline, CodeBuild, and CodeDeploy

## Duration

This workshop will take approximately 2 hours.

## Project Architecture

The Employee Management application consists of two parts: Backend and Frontend. The Backend will use an RDS MySQL database for data storage. The database will be placed in a private zone and run in a multi-AZ setup within the same VPC. The application server will be set up in an Auto Scaling Group and placed behind a Load Balancer. The Frontend will use the Load Balancer to forward requests to the application servers.

![Diagram](/images/1-WorkshopOverview/Workshop-diagram.drawio.png)

### CI/CD for Backend

[The source code for the Backend application](/files/employeemanagmentbackend.zip/) will be stored on GitHub. A trigger will be set up to run the CI/CD pipeline whenever a PUSH action occurs on the main branch. Since the application is written in JAVA, the **CodeBuild** step will use environment variables stored in the **Parameter Store** to build the application into a JAR file. In the CodeDeploy step, the JAR file will be used to deploy the application to the server. CodeDeploy will manage stopping and starting the application.

![Backend-CICD](/images/1-WorkshopOverview/Backend-CICD.drawio.png)

### CI/CD for Frontend

[The source code for the Frontend application](/files/employeemanagement-frontend.zip) will be stored on GitHub. A trigger will be set up to run the CI/CD pipeline whenever a PUSH action occurs on the main branch. Since the application is built on the ReactJS framework, the **CodeBuild** step will build the application into static files such as HTML, CSS, and JavaScript, which are then pushed to an **S3** bucket to run as a website.

![Frontend-CICD](/images/1-WorkshopOverview/Frontend-CICD.drawio.png)
