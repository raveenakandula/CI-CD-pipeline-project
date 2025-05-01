# Section 1: Deploying a Static Website Using AWS S3 and CodePipeline

This section demonstrates how to automate the deployment of a static website (HTML files) using **AWS S3** and **AWS CodePipeline** without using any build or test stage. The pipeline is configured to trigger automatically using **CloudWatch Events** whenever a new version of the website is uploaded.

---

## ✅ Key Services Used

- Amazon S3 (Source and Target Buckets)
- AWS CodePipeline
- Amazon CloudWatch Events

---

## 🛠️ Step-by-Step Procedure

### 1. S3 Buckets Setup

- **Source Bucket**: Created a bucket named `repository-sourcebucket`
  - Enabled **versioning**
- **Target (Website) Bucket**: Created a bucket named `website-targetbucket`
  - Enabled **versioning**
  - Enabled **ACLs**
  - Enabled **Static Website Hosting**
  - Selected option to **block all public access** unchecked (to allow public access)

---

### 2. Creating the CodePipeline

1. Go to **AWS CodePipeline > Create Pipeline**
2. Name the pipeline: `website-deployment`
3. Choose **Custom Pipeline**
4. For **Source Stage**:
   - Source Provider: `Amazon S3`
   - Bucket: `repository-sourcebucket`
   - Object Key: `website.zip` (HTML files must be zipped before uploading)
5. **Trigger**: Selected **CloudWatch Events** to auto-trigger pipeline on file upload
6. **Build Stage**: Skipped (as we are directly deploying HTML files)
7. **Test Stage**: Skipped
8. **Deploy Stage**:
   - Deploy Provider: `Amazon S3`
   - Target Bucket: `website-targetbucket`
   - Enabled: `Extract file before deploy`
   - Canned ACL: `PublicRead`
9. Click **Create Pipeline**

---

### 3. Uploading Website Files

- Uploaded `website.zip` containing `index.html` and `error.html` to the **source bucket**.
- **Important**: The `.zip` file must not be inside any folder. Place both `index.html` and `error.html` directly in the root before zipping.

---

### 4. Execution & Result

- First pipeline run failed because the source file wasn't uploaded yet.
- After uploading `website.zip`, pipeline triggered automatically and ran successfully.
- Enabled static website hosting on the target bucket and accessed the website URL.
- **Version 2.0** page was displayed.
- Made changes to `index.html` for version 5.0, zipped it again, uploaded to the source bucket.
- Pipeline executed again and deployed the new version.
- Refreshed the website – **Version 5.0** appeared successfully.

---

## 🔍 Learnings & Takeaways

- Pipeline automation using S3 and CloudWatch Events is a simple yet powerful approach for static website deployments.
- Folder structure and file placement inside `.zip` files are crucial for successful deployments.
- Versioning helps in managing multiple versions of the website.

---

## 📸 Optional: Folder Contents

You can optionally include a `/website` folder here with sample `index.html` and `error.html` for demo purposes.

---

## 🗃️ Folder Location in Project

