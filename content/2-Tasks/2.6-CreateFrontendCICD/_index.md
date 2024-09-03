---
title : "Create S3 Bucket, CodePipeline, CodeBuild, and CodeDeploy for CICD Frontend"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : "<b>2.6.</b>"
---
## Objectives

This guide will help you perform the following steps:

1. Create a CodeBuild project for the Frontend application.
2. Conduct acceptance testing and verify the application.

## Steps to Implement

### Step 1: Create a CodeBuild Project for the Frontend Application

1. Log in to the **AWS Management Console**.
2. Navigate to **CodeBuild** by searching for `CodeBuild` in the search bar.
3. Click **Create build project**.
4. Enter the **Project name**.
5. In the **Source** section:
   - Select **Source provider**; here, we choose `GitHub`.
   - Connect to your frontend application's source repository.
   - Source version: we choose to enter `main` here.
![CodeBuild-Name](/images/2-TaskList/2.6-CreateFrontendCICD/codebuild-name.png)

6. In the **Primary source webhook events** section:
   - Check **Rebuild every time a code change is pushed to this repository**.
   - In **Filter group**, select **Event type** as `PUSH`: here, we choose the event of pushing additional code to the repository; you can add other events as needed.
![Event-Type](/images/2-TaskList/2.5-CreateBackendCICD/event-type.png)

7. In the **Environment** section:
   - For **Operating system**, choose `Amazon Linux`.
   - For **Runtime**, choose `Standard`.
   - Choose **New service role** to allow CodeBuild to create a new role for you.

   ![Env-Role](/images/2-TaskList/2.6-CreateFrontendCICD/env-role.png)

8. In the **Buildspec** section:
   - Select **Use a buildspec file** (the `buildspec.yml` file in the repository created in the previous step).

9. Click **Create build project**.

10. Update the Permissions for the Service Role created above:
   - Click the link for the Service Role created above.
   - Select **Add Permissions** and choose **Create inline policy**.
   - Add the following policy in JSON format (replace `bucket-frontend` with your frontend bucket):

```json
{
    "Effect": "Allow",
    "Resource": [
        "arn:aws:s3:::bucket-frontend/*",
        "arn:aws:s3:::bucket-frontend"
    ],
    "Action": [
        "s3:PutObject",
        "s3:PutObjectAcl",
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:ListBucket"
    ]
}
```
   - Click **Next**, enter the **Policy Name**, and then select **Create policy**.

### Step 2: Conduct Acceptance Testing and Verify the Application

1. Obtain the DNS from the Load Balancer and add it to the `.env` file of the frontend application (only for the content of this workshop):

   ![lb-dns](/images/2-TaskList/2.6-CreateFrontendCICD/lb-dns.png)

2. Push the updates to your source repository for both the Frontend and Backend.

3. Check if your CodeBuild and CodePipeline are working properly.

   ![deploy-result](/images/2-TaskList/2.6-CreateFrontendCICD/deploy-result.png)

4. Confirm that the application has been successfully deployed.

   ![web](/images/2-TaskList/2.6-CreateFrontendCICD/web.png)

