# Section 3 – Using AWS CodePipeline with Manual Approval and Deploying to a Production S3 Bucket

This section demonstrates how I used **AWS CodePipeline** integrated with **GitHub**, added **Manual Approval**, and deployed to a new **Production S3 Website Bucket**. This setup allows for a review step before deploying to production.

---

## Step-by-Step Process

### 1. Created a New S3 Bucket for Production
- Bucket Name: `production-s3website-bucket`
- Enabled **Static Website Hosting**
- On opening the bucket’s website URL, I encountered a **403 Forbidden Error** (expected, since no deployment yet)

---

### 2. Source Configuration in codepipeline
- Used the same GitHub repo from **Section 2** (instead of CodeCommit).
- The repo contains `index.html` and `error.html`.

---

### 3. Target Configuration in codepipeline
- **Target 1**: Used the same `websit-target-bucket` as before.
- **Target 2**: `production-s3website-bucket` for production deployment.

---

### 4. Added a Manual Approval Stage in codepipeline
- Added a new stage in the pipeline: `ManualApproval`
- Under Actions:
  - **Action Provider**: Manual Approval
  - Created successfully to review changes before production deployment.

---

### 5. Added S3 Deployment Stage for Production Bucket in codepipeline
- Action Name: `S3Deploy`
- Action Provider: **S3**
- Input Artifact: `s3-source`
- Target Bucket: `production-s3website-bucket`
- Additional Configuration:
  - Canned ACL: `public-read`
- Created and linked to the next stage after Manual Approval.

---

### 6. Triggered Pipeline with Code Change
- Changed the `index.html` version text from **1.0 to 4.0**.
- Committed and pushed to GitHub.
- **Result**:
  - Pipeline triggered and updated the `websit-target-bucket` successfully.
  - But **did not deploy to the production bucket** because Manual Approval was pending.

### ![Manual Approval Stoped stage](https://github.com/user-attachments/assets/0061e5db-5999-4d66-aec6-a0e6e113f1e0)

---

### 7. Manually Approved the Stage
- Opened the **CodePipeline console**.
- Approved the Manual Approval stage after reviewing the change.

### ![Maually approved](https://github.com/user-attachments/assets/3b8978f5-c2e0-4bcc-b1a7-e8faf38dcd01)



---

### 8. Deployment Role Error and Fix
- Faced an error: deployment to production S3 bucket failed.
- Troubleshot using **Amazon MQ logs**.
- Created a new IAM Role:  
  `AWSCodePipelineServiceRole-ap-south-1-website-deployment`

After applying the policy, the role was assumed properly and deployment was successful.


### 9. Final Result
Refreshed the Production bucket website URL.

The Forbidden Error was gone.

Website loaded with version 4.0 successfully.

### ![Code pipeline Success](https://github.com/user-attachments/assets/2c4d07de-40d2-4dda-a348-a16107b24ae4)


### ![Website Loaded](https://github.com/user-attachments/assets/5c2dde69-580a-42b7-b0f6-624367f9d9e7)



- Attached **inline policy** with the following permissions:
```json
{
  "Effect": "Allow",
  "Action": [
    "s3:PutObject",
    "s3:PutObjectVersionAcl",
    "s3:PutObjectAcl"
  ],
  "Resource": "arn:aws:s3:::production-s3website-bucket/*"
}




