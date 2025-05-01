## ▶️ Section 1: Deploy Static Website to S3 via AWS CodePipeline

<details>
<summary><strong>▶️ Click to see the process</strong></summary>

<br>

1. **Created two S3 buckets:**
   - `repository-sourcebucket` (source) with versioning enabled.
   - `website-targetbucket` (target) with:
     - ACLs enabled
     - Public access block disabled
     - Versioning enabled

2. **Created CodePipeline** named `website-deployment`:
   - Source: S3 (`repository-sourcebucket`) with `website.zip` as object key
   - Trigger: CloudWatch Events (auto-triggers pipeline on object change)
   - Skipped build and test stage
   - Deploy stage: S3 (`website-targetbucket`) with "Extract file before deploy" enabled and canned ACL set to `public-read`

3. **Enabled static website hosting** on `website-targetbucket`.

4. Uploaded `website.zip` (containing `index.html` and `error.html`) to source bucket.

5. Pipeline auto-triggered → Deployment successful → Static website loaded.

6. **Tested update flow**:
   - Updated `index.html` to version 5.0
   - Re-zipped and uploaded → Pipeline triggered again
   - Website reflected new version (5.0) instantly

</details>


![Screenshot 2025-05-01 131925](https://github.com/user-attachments/assets/7ab05a94-b231-4c82-9e6e-e22487c2bbc9)

