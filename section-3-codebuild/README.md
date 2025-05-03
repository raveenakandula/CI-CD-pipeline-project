#  Angular Project Deployment with AWS CodePipeline & S3

This project demonstrates the CI/CD pipeline setup for an Angular application using **AWS CodePipeline**, **CodeBuild**, and **S3 static website hosting**. Due to CodeCommit limitations in new AWS accounts, S3 was used as the source instead of CodeCommit.

---

## Project Overview

- **Framework:** Angular
- **CI/CD Tools:** AWS CodePipeline, CodeBuild
- **Hosting:** Amazon S3 (Static Website Hosting)

---

## Step-by-Step Workflow

### 1. Upload Angular Project to S3

- Compressed the Angular project files into a `.zip`
- Uploaded the `.zip` to a newly created S3 bucket

### 2. Initial Build Setup

- Created a **CodeBuild** project
- Triggered the build for the first time → **Failed** due to missing `buildspec.yml`

### 3. CodePipeline Configuration

- Created a **Custom Pipeline**:
  - **Source:** S3 (Angular `.zip` file)
  - **Build:** CodeBuild project
- Pipeline automatically triggered and failed at the build stage

---

##  Fixing Build Issues

### 4. Hosting Bucket Creation

- Created a new S3 bucket: `s3-bucket-1-raveena`
- Enabled **Static Website Hosting**
- Attached a **Bucket Policy** to allow `s3:GetObject` access for public read

### 5. Adding `buildspec.yml`

- Referred to AWS documentation: [Build Specification Reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)
buildspec.yml file code was at last

 As i got an error while triggering pipeline at Build stage so i did the folder adjustemnts
 
### 6. Folder Structure Adjustment
Removed unnecessary nested folder layers

Recompressed the updated files into a .zip

Re-uploaded the new zip to the S3 source bucket

### 7. Successful Build Execution
Triggered the pipeline again

"Build stage succeeded"

![Pipeline succes ](https://github.com/user-attachments/assets/f42a8b7c-a7a7-4de0-8f85-e6d08f2bd2c3)


Logs confirmed execution of each phase (install, pre_build, build, post_build)

### 8. Deployment Verification
* All built artifacts deployed to s3-bucket-1-raveena

![Recieved files in s3 bucket](https://github.com/user-attachments/assets/75cb4cdf-01ea-48ce-bfea-f0701ab86855)


* Accessed the static website URL of the bucket

* Website loaded successfully with the message:

## $${\color{darkgreen}"Sample  Angular App For AWS CodePipeline"}$$



![Website loaded result](https://github.com/user-attachments/assets/8637ff0f-c9a9-4ee7-80e7-8a413afe810f)


### Conclusion
Successfully implemented a CI/CD pipeline for an Angular application using AWS services. Despite initial CodeCommit limitations, the solution was adapted to use S3, ensuring automated build and deployment via AWS CodePipeline.

---
- Created the following `buildspec.yml` file code in VS Code:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 12
    commands:
      - npm install -g @angular/cli@9.0.6

  pre_build:
    commands:
      - npm install

  build:
    commands:
      - ng build --prod

 post_build:
   commands:
     - aws s3 cp dist/my-angular-project s3://s3-bucket-1-raveena --recursive