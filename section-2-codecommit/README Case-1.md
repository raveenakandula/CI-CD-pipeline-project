# Section 2: Integrating GitHub with AWS CodePipeline

In this section, I demonstrate how to use **AWS CodePipeline** to automate the deployment of a static website, using **GitHub** as the source repository. Although the ideal method is using **AWS CodeCommit**, my AWS account did not have access to it, so I connected **GitHub** to AWS CodePipeline using OAuth.

---

## Key Services Used

- AWS CodePipeline
- GitHub (OAuth connection)
- Amazon S3 (Target bucket for the static website)

---

##  Step-by-Step Procedure

### 1. Initial Setup & Problem Encountered

- Initially, I intended to use **AWS CodeCommit** as the source for CodePipeline. However, AWS CodeCommit was unavailable for my account, and I faced issues while setting it up.
- After researching the problem, I found that **GitHub** can be connected to AWS CodePipeline using **OAuth** authentication, and I successfully resolved the issue by connecting my GitHub repository to CodePipeline.

You can follow the official documentation for setting up the GitHub connection with CodePipeline here:  
[GitHub Connection Documentation](https://docs.aws.amazon.com/codepipeline/latest/userguide/connections-github.html)

---

### 2. Connecting GitHub to AWS CodePipeline

1. I used an existing **AWS CodePipeline** and modified it.
2. In the **Source Stage**, I changed the source to **GitHub** via **OAuth** authentication.
3. I configured the GitHub repository with two files: `index.html` and `error.html` as part of the pipeline.
   - These files are stored in the GitHub repository and will be deployed to the **S3 Target Bucket** (configured in the pipeline).

---

### 3. CodePipeline Execution

1. On executing the pipeline for the first time, it deployed the website successfully with **Version 5.0** as the default page.
2. To test the update, I made a change to the `index.html` file in my local VS Code (changing the version from **5.0** to **1.0**).
3. I committed the change using Git (`git commit -m "Update version to 1.0"`) and pushed it to GitHub (`git push`).
4. CodePipeline automatically triggered and fetched the updated content from GitHub.
5. However, when I refreshed the website page, it displayed **Version 1.0** without having to trigger the pipeline manually again.

---

### 4. Key Learnings & Takeaways

- Connecting GitHub to CodePipeline is simple once the OAuth connection is established.
- The GitHub repository acts as the source, and the changes pushed from the local Git repository are automatically reflected in the website hosted on S3 after the CodePipeline triggers.
- Using GitHub as a source repository allows flexibility and quick code updates.

---




