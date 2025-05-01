# Section 3 – Using AWS CodePipeline with Manual Approval and Deploying to a Production S3 Bucket

This section demonstrates how I used **AWS CodePipeline** integrated with **GitHub**, added **Manual Approval**, and deployed to a new **Production S3 Website Bucket**. This setup allows for a review step before deploying to production.

---

## 🔧 Step-by-Step Process

### 1. Created a New S3 Bucket for Production
- Bucket Name: `production-s3website-bucket`
- Enabled **Static Website Hosting**
- On opening the bucket’s website URL, I encountered a **403 Forbidden Error** (expected, since no deployment yet).

---

### 2. Source Configuration
- Used the same GitHub repo from **Section 2** (instead of CodeCommit).
- The repo contains `index.html` and `error.html`.

---

### 3. Target Configuration
- **Target 1**: Used the same `websit-target-bucket` as before.
- **Target 2**: `production-s3website-bucket` for production deployment.

---

### 4. Added a Manual Approval Stage
- Added a new stage in the pipeline: `ManualApproval`
- Under Actions:
  - **Action Provider**: Manual Approval
  - Created successfully to review changes before production deployment.

---

### 5. Added S3 Deployment Stage for Production Bucket
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

---

### 7. Manually Approved the Stage
- Opened the **CodePipeline console**.
- Approved the Manual Approval stage after reviewing the change.

---

### 8. Deployment Role Error and Fix
- Faced an error: deployment to production S3 bucket failed.
- Troubleshot using **Amazon MQ logs**.
- Created a new IAM Role:  
  `AWSCodePipelineServiceRole-ap-south-1-website-deployment`
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

