# Java Project CI/CD with AWS CodePipeline & Lambda

This project demonstrates how to automate the build and deployment of a Java application to an AWS Lambda function using **AWS CodePipeline**, **CodeBuild**, and **S3**. Due to CodeCommit limitations, S3 was used as the code source.

---

##  Project Overview

- **Language:** Java 11
- **Build Tool:** Maven
- **Deployment Target:** AWS Lambda
- **CI/CD Services:** AWS S3, CodeBuild, CodePipeline

---

##  Step-by-Step Implementation

### 1. Upload Java Project to S3

- Created a new S3 bucket
- Uploaded the zipped project as: `Java-project.zip`

### 2. Create a CodeBuild Project

- Created a **Build Project**
- Selected **S3** as the source
  - Provided the bucket name and key as `Java-project.zip`

---

## 3. Buildspec Configuration
 Buildspec-java.yml commands are below 

Lambda Setup
4. Created AWS Lambda Function
Created a new Lambda function with runtime: Java 11

Note: The code for this Lambda is not directly visible in the console as it is packaged with external dependencies.

5. Linked Lambda with Build Process
Ensured the Lambda function name JavaLambdaB8 matches the one referenced in the buildspec-java.yml file.

IAM Role Permissions
6. CodeBuild Role Permissions
Navigated to the CodeBuild Project > Project Details

Clicked the Service Role

Attached the AWSLambda_FullAccess managed policy to allow Lambda updates from CodeBuild

Pipeline Execution
7. Buildspec Name Configuration
Encountered a build error initially due to missing buildspec reference

Resolved it by explicitly setting buildspec-java.yml in the Buildspec Name field of the CodeBuild project

8. Final Pipeline Result
Triggered the pipeline

Build stage and post-build Lambda deployment executed successfully

Conclusion
This project illustrates a complete pipeline for building a Java application and deploying it to AWS Lambda using CodePipeline and CodeBuild. Using S3 as the source and proper IAM permissions, the automation flow was achieved seamlessly.



###  Added `buildspec-java.yml`

Created a build specification file named `buildspec-java.yml` with the following content:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto11

  pre_build:
    commands:
      - cd HelloWorldDemo
      - mvn clean

  build:
    commands:
      - mvn -Dmaven.test.skip package  # Skipped test cases

  post_build:
    commands:
      - aws lambda update-function-code \
          --function-name JavaLambdaB8 \
          --zip-file fileb://target/demo-1.0.0.jar \
          --region ap-south-1
