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

## 4. Created AWS Lambda Function
Created a new Lambda function with runtime: Java 11

Note: The code for this Lambda is not directly visible in the console as it is packaged with external dependencies.

## 5. Linked Lambda with Build Process
Ensured the Lambda function name Javafunction matches the one referenced in the buildspec-java.yml file.


## 6. CodeBuild Role Permissions
Navigated to the CodeBuild Project > Project Details

Clicked the Service Role

Attached the AWSLambda_FullAccess managed policy to allow Lambda updates from CodeBuild

Pipeline Execution
## 7. Buildspec Name Configuration
Encountered a build error initially due to missing buildspec reference

Resolved it by explicitly setting buildspec-java.yml in the Buildspec Name field of the CodeBuild project

## 8. Final Pipeline Result
Triggered the pipeline successfully

Build stage and post-build Lambda deployment executed successfully

![Pipeline success](https://github.com/user-attachments/assets/3263f1d3-0501-4c8a-9da5-981b1381cb95)


## Conclusion
Hence this project shows how to build and deploy a Java application to AWS Lambda using CodePipeline and CodeBuild. By using S3 to store the project files and giving the right permissions, the entire process was successfully automated.



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
          --function-name Javafunction \
          --zip-file fileb://target/demo-1.0.0.jar \
          --region ap-south-1
