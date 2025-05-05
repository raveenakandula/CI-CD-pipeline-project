# Angular Project Deployment to EC2 using AWS CodeDeploy

This project demonstrates how to deploy an Angular application to EC2 instances using AWS services including S3, CodeBuild, CodePipeline, and CodeDeploy.

---

## Step 1: EC2 Instances Setup

1. **Created EC2 Instances**
   - Launched two EC2 instances.
   - Instance name: `CodeDeployServer`.
   - Tags: `Application: Angular`.
   - OS: Amazon Linux (kernel 5.10), t2.micro.
   - Security Group: Port 80 enabled.
   - Storage: gp3.
   - IAM Role: Created a new role and attached:
     - `AmazonEC2RoleForAWSCodeDeploy` (includes S3 and CodeDeploy permissions).

---

## Step 2: Installing CodeDeploy Agent

**Search Reference**:  
Google → `aws code deploy agent installation`  
URL → https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html

### For Instance 1 (Region: ap-south-1)
```bash
sudo yum update -y
sudo yum install ruby wget -y
cd /home/ec2-user
wget https://aws-codedeploy-ap-south-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent start
sudo service codedeploy-agent status bash
```
## For Instance 2 (Region: ap-south-1)
```bash
sudo yum update -y
sudo yum install ruby wget -y
cd /home/ec2-user
wget https://aws-codedeploy-ap-south-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent start
sudo service codedeploy-agent status
```
## Step 3: Install Apache (HTTPD) (On Both Instances)
```bash
sudo yum install httpd -y
sudo service httpd status
sudo service httpd start
sudo chkconfig httpd on
```
## Step 4: Create CodeDeploy Application

* Go to AWS CodeDeploy.

* Create a new application:

* Name: AngularDeployment

* Compute platform: EC2

### Deployment Group:

* Name: AngularServers

* Created a separate IAM role for CodeDeploy.

* Chose the Application=Angular tag for instance selection.

* Selected "Never install CodeDeploy agent" (manually installed).

* Created the deployment group.

## Step 5: CodePipeline Setup

Added a New Stage in CodePipeline

Stage name: EC2Deploy

Selected the previously created application (AngularDeployment) and deployment group (AngularServers).

## Step 6: AppSpec and BuildSpec Configuration
### Created appspec.yml

First Deployment Failed due to missing appspec.yml.

Followed AWS documentation to create one:

https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html

Based on AWS documentation:

### appspec.yml Content:
```bash
version: 0.0
os: Linux
files:
  source: ./dist/angular-project
  destination: /var/www/html

permissions:
  - object: /var/www/html
    pattern: "**"
    mode: '0755'
    owner: root
    group: root
    type:
      - file
      - directory
```
## Step 7: Updated buildspec.yml to Include AppSpec
Modified the existing buildspec.yml file to include processing of appspec.yml during build.

Added this Artifacts in buildspec.yml
```bash
artifacts:
  files:
    - 'dist/my-angular-project/**/*'
    - appspec.yml
    - 'deploy-scripts/**/*'
```
Ensured the zipped project included buildspec.yml and appspec.yml
## Step 8: Role Policy Update
Errors encountered related to insufficient permissions for deployment creation.

Updated IAM policy of CodeBuild project role to include:in Actions field

codedeploy:GetDeploymentConfig

codedeploy:CreateDeployment

codedeploy:GetDeployment

Added these actions in a custom policy and attached it to the CodeBuild service role.

## Step 9: Successful Deployment
After updating permissions, the pipeline executed successfully:
Pipeline stages: Source (S3) → Build (CodeBuild) → Deploy (CodeDeploy to EC2).

![Pipeline](https://github.com/user-attachments/assets/dbe49448-5f45-4b35-a430-6b4cf20af1bf)


Deployment successful to two EC2 instance.

![deployed tom2 instances successfully](https://github.com/user-attachments/assets/814afc18-1873-4829-8551-3f4c7b168419)


Public URL of EC2 instance displays:
"Sample Angular App for AWS CodePipeline"

![angular websiste loaded](https://github.com/user-attachments/assets/1d470f48-e493-4d46-bd5a-6731f1f07e45)


## Conclusion
This deployment process shows how to integrate EC2, CodeDeploy, and CodePipeline to deliver Angular applications efficiently in a CI/CD pipeline.






