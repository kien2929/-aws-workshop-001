---
title : "Create CodePipeline, CodeBuild, and CodeDeploy for CICD Backend"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : "<b>2.5.</b>"
---
## Introduction

AWS offers a set of developer tools that help automate the process of building, deploying, and managing applications. Among these tools, **AWS CodeBuild**, **AWS CodeDeploy**, and **AWS CodePipeline** play an important role in setting up Continuous Integration and Continuous Deployment (CI/CD) pipelines.

- **AWS CodeBuild**: A fully managed build service that compiles source code, runs tests, and produces software packages that are ready to deploy. CodeBuild eliminates the need to provision, manage, and scale your own build servers.

- **AWS CodeDeploy**: A service that automates code deployments to any server, including Amazon EC2 instances and on-premises servers. It simplifies the deployment process and helps maintain high availability.

- **AWS CodePipeline**: A fully managed continuous delivery service that automates the build, test, and deploy phases of your release process. It integrates with CodeBuild, CodeDeploy, and other AWS services to provide a seamless CI/CD experience.

## Objective

This guide will help you perform the following steps:

1. Create a CodeBuild project for the backend application.
2. Set up CodeDeploy to deploy the application to EC2 instances.
3. Create a CodePipeline to orchestrate the CICD process.

## Steps to Set Up CodeBuild, CodeDeploy, and CodePipeline

### Step 1: Create a CodeBuild Project for the Backend Application

1. Log in to the **AWS Management Console**.
2. Navigate to **CodeBuild** by searching for `CodeBuild` in the search bar.
3. Click **Create build project**.
4. Enter the **Project name**.
5. In the **Source** section:
   - Select **Source provider**, here we choose `GitHub`.
   - Connect to your backend application's source repository.
   - Source version: here, select `main`.
![CodeBuild-Name](/images/2-TaskList/2.5-CreateBackendCICD/codebuild-name.png)
6. In the **Primary source webhook events** section:
   - Check **Rebuild every time a code change is pushed to this repository**.
   - In **Filter group**, select **Event type** as `PUSH`: Here, I choose the event for pushing new code to the repository. You can add other events as needed.
![Event-Type](/images/2-TaskList/2.5-CreateBackendCICD/event-type.png)
7. In the **Environment** section:
   - For **Operating system**, select `Amazon Linux`.
   - For **Runtime**, select `Standard`.
   - Choose **New service role** to let CodeBuild create a new role for you.
![Env-Role](/images/2-TaskList/2.5-CreateBackendCICD/env-role.png)
8. In the **Buildspec** section:
   - Select **Use a buildspec file** (the `buildspec.yml` file in the repository created in the previous step).
9. Click **Create build project**.
10. Update Permissions for the newly created Service Role:
   - Click on the link for the newly created Service Role.
![Link-Role](/images/2-TaskList/2.5-CreateBackendCICD/link-role.png)
   - Select **Add Permissions** and choose **Create inline policy**.
![Inline-Policy](/images/2-TaskList/2.5-CreateBackendCICD/inline-policy.png)
   - Add the following policy in JSON format (replace `010928203475` with your Account ID):
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
   - Click **Next**, enter **Policy Name**, and then select **Create policy**.

   ![Create-Policy](/images/2-TaskList/2.5-CreateBackendCICD/create-policy.png)

### Step 2: Set Up CodeDeploy to Deploy the Application to EC2 Instances

1. Navigate to **CodeDeploy** in the **AWS Management Console**.
2. Click **Applications** and select **Create application**.
3. Enter the **Application name** and choose a **Compute platform**: `EC2/On-premises`.
4. Click **Create application**.

   ![Create-App](/images/2-TaskList/2.5-CreateBackendCICD/create-application.png)

5. Before creating a **deployment group**, create a Role for CodeDeploy:
   - Go to the **IAM Dashboard**, select **Roles**, and click **Create role**.
   - Choose **AWS Service**, then select **CodeDeploy**.
   - Ensure **Permission policies** include **AWSCodeDeployRole**.
   - Name the new role (e.g., **CodeDeployServiceRole**) and select **Create role**.

6. Return to the CodeDeploy Application, select **Create deployment group**.
7. Enter **Deployment group name**.
8. Select the **Service role** that we just created.
9. In **Deployment type**, choose `In-place` or `Blue/Green`.

   ![Deployment-Group](/images/2-TaskList/2.5-CreateBackendCICD/deployment-group.png)

10. In **Environment configuration**, select the previously created **Auto Scaling groups**.
11. In **Deployment settings**, select `CodeDeployDefault.OneAtATime`.

   ![Deployment-Setting](/images/2-TaskList/2.5-CreateBackendCICD/deployment-settings.png)

12. Click **Create deployment group**.

### Step 3: Create a CodePipeline to Orchestrate the CICD Process

1. Navigate to **CodePipeline** in the **AWS Management Console**.
2. Click **Create pipeline**.
3. Enter the **Pipeline name** and select **New service role** under **Service role**.

   ![codepipeline](/images/2-TaskList/2.5-CreateBackendCICD/codepipeline.png)

4. In the **Source stage**:
   - Choose **Source provider**: `GitHub`.
   - Select the **Repository** and **Branch** you want to use.
   - Click **Next**.

   ![source](/images/2-TaskList/2.5-CreateBackendCICD/git-source.png)

5. In the **Build stage**:
   - Choose **AWS CodeBuild** as the **Build provider**.
   - Select the **CodeBuild project** created in Step 1.
   - Click **Next**.

   ![build-step](/images/2-TaskList/2.5-CreateBackendCICD/build-step.png)

6. In the **Deploy stage**:
   - Choose **AWS CodeDeploy** as the **Deploy provider**.
   - Select the **Application name** and **Deployment group** created in Step 2.
   - Click **Next**.

   ![deploy-step](/images/2-TaskList/2.5-CreateBackendCICD/deploy-step.png)

7. Review the pipeline settings and click **Create pipeline**.

## Conclusion

By following the above steps, you have successfully set up **CodeBuild**, **CodeDeploy**, and **CodePipeline** on AWS. This configuration allows you to establish a robust CI/CD pipeline that automates build, test, and deployment processes, thereby improving development efficiency and minimizing deployment errors.

AWS provides a powerful set of tools to build, deploy, and manage applications on the cloud. Combining these services enables you to create an automated and scalable CI/CD pipeline, ensuring a fast and reliable software release process.

