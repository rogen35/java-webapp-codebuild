# 🚀 AWS CodeBuild Setup: Java Maven Web App (Corretto 11)

This project demonstrates how to build a Java web application using AWS CodeBuild, Maven, and CodeArtifact, with Java Corretto 11.

---

## 🔧 What This Project Does

- Automatically builds your app when you push to GitHub.
- Connects Maven to AWS CodeArtifact.
- Uses CodeBuild to compile and package your Java app into a `.war` file.
- Follows CI/CD best practices.

---

## 📝 CodeBuild Configuration Steps

### 1. **Create CodeBuild Project**
- Go to **AWS CodeBuild > Create Build Project**
- Set **Project name** to `rogen-devops-cicd`
- Under **Source**, choose GitHub and connect your repo via GitHub App

### 2. **Environment Settings**
- Environment image: `aws/codebuild/amazonlinux-x86_64-standard:corretto11`
- Use **On-Demand image**
- Choose **Amazon Linux**, **Standard**, then select **Corretto 11**

### 3. **Artifacts and Logging**
- Store artifacts in an S3 bucket (Zip format)
- Enable CloudWatch logs

---

## 🧾 buildspec.yml

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto11
    commands:
      - echo "Getting CodeArtifact auth token..."
      - export CODEARTIFACT_AUTH_TOKEN=$(aws codeartifact get-authorization-token --domain nextwork --domain-owner 811259913624 --region ap-southeast-1 --query authorizationToken --output text)
      - echo "Token received."
  pre_build:
    commands:
      - echo "Setting up Maven auth..."
  build:
    commands:
      - echo "Running Maven build..."
      - mvn clean install --settings settings.xml

artifacts:
  files:
    - target/*.war
```

✅ **Explanation**:
- Authenticates to CodeArtifact
- Runs Maven build
- Stores output `.war` file

---

## ⚙️ settings.xml

```xml
<settings>
  <servers>
    <server>
      <id>nextwork-rogen-devops-cicd</id>
      <username>aws</username>
      <password>${env.CODEARTIFACT_AUTH_TOKEN}</password>
    </server>
  </servers>
</settings>
```

🔐 **Used for Maven to authenticate with CodeArtifact**

---

## 🧱 pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.rogen.app</groupId>
  <artifactId>rogen-web-project</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>rogen-web-project Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <repositories>
    <repository>
      <id>nextwork-rogen-devops-cicd</id>
      <url>https://nextwork-811259913624.d.codeartifact.ap-southeast-1.amazonaws.com/maven/rogen-devops-cicd/</url>
    </repository>
  </repositories>

  <pluginRepositories>
    <pluginRepository>
      <id>nextwork-rogen-devops-cicd</id>
      <url>https://nextwork-811259913624.d.codeartifact.ap-southeast-1.amazonaws.com/maven/rogen-devops-cicd/</url>
      <releases><enabled>true</enabled></releases>
      <snapshots><enabled>true</enabled></snapshots>
    </pluginRepository>
  </pluginRepositories>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <finalName>rogen-web-project</finalName>
  </build>
</project>
```

---

## 🔐 IAM Policy for CodeArtifact Access

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codeartifact:GetAuthorizationToken",
        "codeartifact:GetRepositoryEndpoint",
        "codeartifact:ReadFromRepository"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "sts:GetServiceBearerToken",
      "Resource": "*"
    }
  ]
}
```

This policy is attached to the IAM role used by your EC2 instance or CodeBuild.

---

## ✅ Result
- A `.war` file is generated inside the `target/` folder
- Ready for deployment via Elastic Beanstalk or CodeDeploy

---

## 👨‍💻 Author
Created as part of a hands-on DevOps CI/CD learning project using AWS.