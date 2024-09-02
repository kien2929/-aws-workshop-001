---
title : "Create YML configuration files for Frontend and Backend"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : "<b>2.3.</b>"
---
## Introduction

AWS CodeBuild and CodeDeploy are two important services in the AWS CI/CD toolset that help you automate the process of building and deploying applications. Using these two services together can accelerate development, improve code quality, and reduce deployment risks.

- **AWS CodeBuild** is a fully managed and scalable continuous integration (CI) service that can compile source code, run tests, and create ready-to-deploy packages.
- **AWS CodeDeploy** is a continuous deployment (CD) service that automates the deployment of source code to Amazon EC2 instances, AWS Fargate, AWS Lambda, and even on-premises servers.

## Objectives

In this guide, we will perform the following steps:

1. Create an S3 bucket to host the website for the Frontend.
2. Set up a CodeBuild configuration file for the Frontend.
3. Add the necessary parameters for the Backend build step into the Parameter Store.
4. Set up a CodeBuild configuration file for the Backend.
5. Set up a CodeDeploy configuration file for the Backend.

## Steps to Perform:

### Step 1: Create an S3 Bucket to Host the Website for the Frontend.

1. Create a new S3 Bucket:
   - Log in to the **AWS Management Console**.
   - Navigate to **Amazon S3** by searching for "S3" in the search bar.
   - Click **Create bucket**.
   ![Create Bucket](/images/2-TaskList/2.3-CreateYamlFile/create-bucket.png)
   - Configure the bucket settings:
     - **Bucket name**: Enter a unique name for your bucket (e.g., `my-frontend-website-bucket`).
     - **Region**: Choose a suitable region (e.g., `us-east-1`).
   - Leave the other options as default.
   - Uncheck **Block all public access**.
   - Select **I acknowledge that the current settings might result in this bucket and the objects within becoming public**.
   - Click **Create bucket**.

2. Configure the S3 bucket for static website hosting:
   - Select the newly created bucket.
   - Navigate to the **Properties** tab.
   - Scroll down and click **Edit** under **Static website hosting**.
   - Select **Enable**.
   - Under **Index document**, enter the name of your main page file, e.g., `index.html`.
   - Under **Error document**, you can enter `error.html` or leave it blank if there is no custom error page.
   - Click **Save changes**.

3. Add a bucket policy to allow public access:
   - Navigate to the **Permissions** tab.
   - Scroll down to the **Bucket policy** section and click **Edit**.
   - Add the following JSON policy:

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
   - Replace `my-frontend-website-bucket` with your bucket name.
   - Click **Save changes**.

### Step 2: Set Up the CodeBuild Configuration File for Frontend.
   - In the root directory of your project folder, add a file named buildspec.yml:

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
   - Replace `my-frontend-website-bucket` with your bucket name.
   - With the above configuration file, when **CodeBuild** runs, it will choose the `nodejs` environment and perform the project build steps. Finally, it will upload the built files to your frontend bucket.

### Step 3: Add the Necessary Parameters for the Backend Build Step into the Parameter Store.
   - Log in to the **AWS Management Console**.
   - Navigate to **Parameter Store** by searching for `Parameter Store` in the search bar.
   - Click **Create parameter**.
![Create-Parameter](/images/2-TaskList/2.3-CreateYamlFile/create-parameter.png)
   - Enter Name as **DB_URL**.
   - Enter Value as jdbc:mysql://my-mysql-instance.c3e6oeawqqnk.us-east-1.rds.amazonaws.com:3306/employee_management. Here, `my-mysql-instance.c3e6oeawqqnk.us-east-1.rds.amazonaws.com` and `employee_management` are the **endpoint** and **Initial database name** of the RDS instance created in the previous step.
   - Similarly, create parameters **DB_USER** and **DB_PASSWORD** with values corresponding to the **Master username** and **Master password** of the RDS instance created in the previous step.

### Step 4: Set Up the CodeBuild Configuration File for Backend.
   - In the root directory of your project folder, add a file named `buildspec.yml`:
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
   - With the above configuration file, when **CodeBuild** runs, it will perform the following steps:
    - Choose the Java 17 environment and install AWS CLI.
    - Retrieve the parameters from **Parameter Store** and add them to the `application.properties` file.
    - Build the project to a `jar` file.
    - Save the `target/*.jar`, `appspec.yml`, and `scripts` files for use in the deployment step.

### Step 5: Set Up the CodeDeploy Configuration File for Backend.
   - In the root directory of your project folder, add a file named appspec.yml:
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
   - In the root directory of your project folder, create a scripts directory and add a file named deploy.sh with the following content:
```sh
#!/bin/bash

# Find the latest JAR file in the target directory
JAR_FILE=$(ls -t /home/ec2-user/app/*.jar | head -n 1)

# Stop the current application if there is one running
sudo pkill -f 'java -jar' || true

# Start the new application from the found JAR file in the background
nohup java -jar "$JAR_FILE" > /dev/null 2>&1 &

# Print success message
echo "The application has been restarted successfully."
```
   - With the above configuration files, when CodeDeploy runs, it will perform the following steps:
    - Retrieve the .jar file from the build step and place it in the /home/ec2-user/app directory.
    - After obtaining the files from the build step, it will deploy according to the scripts/deploy.sh file.
