# 🔐 Secure Packages with AWS CodeArtifact – CI/CD Project

**Author:** Rogen Magdasal G  
**Platform:** [NextWork.org](https://community.nextwork.org)  
**Project Part:** 3 of DevOps CI/CD Series

---

## 🎯 Project Objective

In this project, I will demonstrate how to connect **Maven** to **AWS CodeArtifact** and build a web app using **AWS CodeBuild**.

> I'm doing this project to learn how to manage dependencies securely and automate builds as part of a CI/CD pipeline.

---

## 🧰 Services & Concepts Used

- **Services:** EC2, CodeArtifact, CodeBuild, IAM, GitHub
- **Key Concepts:** CI/CD setup, IAM roles, Maven configuration with CodeArtifact

---

## 📌 Project Reflection

> This project took me approximately a few hours. The most challenging part was fixing permission and Maven setup issues.  
> It was most rewarding to see the build succeed and packages appear in CodeArtifact.

🧱 This is part 3 of a DevOps CI/CD series. Next, I will deploy my web app using **AWS CodeDeploy**.

---

## 📦 What is AWS CodeArtifact?

AWS CodeArtifact is a secure, scalable, and fully managed artifact repository for managing dependencies like libraries and packages.

- **Domains** are used to manage permissions across multiple repositories.
- **Upstream Repositories** allow fetching packages from external sources when not available locally.

---

## 🔐 CodeArtifact Security & IAM

### 🧾 Issue: Authorization Token

> "You need an authorization token to access CodeArtifact because AWS wants to be sure that you are allowed to read from or write to that private package repository."

### ✅ Resolution

I resolved the token error by attaching the correct IAM **role** to my EC2 instance.

IAM **roles** are best practice because they:
- Provide temporary credentials
- Follow the **least privilege** principle
- Eliminate hardcoded credentials

---

## 📜 IAM Policy Sample

The policy grants access to CodeArtifact while enforcing scoped permissions.

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

---

## ⚙️ Maven & CodeArtifact

To test Maven’s connection to CodeArtifact, I compiled the project using a custom `settings.xml` file.

### 🧾 What does `settings.xml` do?

It configures Maven with the repository URL and credentials so it can securely pull packages.

---

## 🛠 What is Compiling?

> Compiling means converting human-readable source code into machine-readable bytecode or executables.

---

## ✅ Verification

After compiling, I checked the CodeArtifact repository. It correctly stored my project’s dependencies — confirming the integration was successful.

---

## 🌐 Learn More

NextWork is the place to learn and showcase your DevOps skills.  
Visit [nextwork.org](https://community.nextwork.org) to explore more hands-on projects.