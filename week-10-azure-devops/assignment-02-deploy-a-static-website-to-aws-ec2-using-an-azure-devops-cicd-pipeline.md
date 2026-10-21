# Assignment 2 — Deploy A Static Website to AWS EC2 Using an Azure DevOps CI/CD Pipeline

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will import and personalize the Static Website, provision and configure an AWS EC2 instance using Terraform and Ansible, and create an Azure DevOps CI/CD pipeline that automatically deploys the website to Nginx through an SSH Service Connection.

---

# Task 0 — Verify the Existing Tooling and Self-Hosted Agent

## Goal

Confirm that Terraform, Ansible, AWS CLI, SSH, and the self-hosted Azure Pipelines agent are ready.

No submission screenshot is required for this task.

---

# Task 1 — Import and Personalize the Azure Static Website Repository

## Goal

Import the Azure Static Website into Azure Repos and add your Full Name to the website.

## Evidence

### Screenshot 1 — Azure Static Website in Azure Repos

Add a screenshot of Azure Repos showing:

* Imported Azure Static Website repository
* Project files
* `index.html`

![ouput](./screenshots/wk10a2t1-ss1.png)

---

# Task 2 — Provision and Configure the Target EC2 Instance

## Goal

Provision the AWS EC2 instance using Terraform and configure Nginx, SSH access, and deployment permissions using Ansible.

No additional submission screenshot is required for this task.

---

# Task 3 — Create the SSH Service Connection

## Goal

Create an Azure DevOps SSH Service Connection that can connect to the target EC2 instance using your selected SSH authentication method.

## Evidence

### Screenshot 2 — SSH Service Connection

Add a screenshot of the saved SSH Service Connection **Overview** page showing:

* Service Connection name
* SSH connection type

![ouput](./screenshots/wk10a2t3-ss2.png)

> Do not expose a password, SSH private key, passphrase, or another credential.

---

# Task 4 — Create the Azure DevOps YAML Pipeline

## Goal

Create an Azure DevOps YAML pipeline that deploys the Azure Static Website to the target EC2 instance after a commit is pushed.

## Evidence

### Screenshot 3 — Azure Pipelines YAML

Add a screenshot of `azure-pipelines.yml` open in the Azure Repos editor showing:

* Push trigger
* Selected self-hosted agent pool
* Pipeline variables
* Repository checkout step
* Pipeline information step
* `CopyFilesOverSSH@0` task
* `SSH@0` verification task

![ouput](./screenshots/wk10a2t4-ss3.png)
![ouput](./screenshots/wk10a2t4-ss3a.png)
![ouput](./screenshots/wk10a2t4-ss3b.png)
![ouput](./screenshots/wk10a2t4-ss3c.png)

> Ensure that no password, SSH private key, PAT, or AWS credential is visible.

---

# Task 5 — Create, Authorize, and Run the Pipeline

## Goal

Run the Azure DevOps pipeline and confirm that the website files are transferred and verified successfully.

## Evidence

### Screenshot 4 — Successful Pipeline Run

Add a screenshot of the successful pipeline run and log summary showing:

* Overall pipeline status as **Succeeded**
* Pipeline information step completed
* File-copy step completed
* Remote-verification step completed
* Your Full Name visible in the pipeline output

![ouput](./screenshots/wk10a2t5-ss4.png)
![ouput](./screenshots/wk10a2t5-ss4a.png)
![ouput](./screenshots/wk10a2t5-ss4b.png)
![ouput](./screenshots/wk10a2t5-ss4c.png)

---

# Task 6 — Verify the Website and Automatic Trigger

## Goal

Confirm that the website is accessible through the EC2 public IP address and that a new pushed commit automatically triggers another deployment.

## Evidence

### Screenshot 5 — Deployed Azure Static Website

Add a browser screenshot showing:

* Deployed Azure Static Website
* EC2 public IP address in the browser address bar
* Your Full Name
* Updated website content after the automatic deployment

![ouput](./screenshots/wk10a2t6-ss5.png)
![ouput](./screenshots/wk10a2t6-ss5a.png)

## Final Website URL

**http://35.172.100.179/**

---

# Assignment Summary

Write a short summary of the completed CI/CD workflow.

CI/CD workflow summary: Azure Static Website to AWS EC2

The workflow deploys a static website from Azure Repos to an Nginx web server on AWS EC2, using a self-hosted Azure Pipelines agent.

1. Infrastructure (Terraform): created an Ubuntu 22.04 EC2 instance (35.172.100.179) with its own VPC, subnet, internet gateway and key pair. The security group opens port 22 (SSH) and port 80 (HTTP).
2. Server setup (Ansible): site.yml installed and enabled Nginx, gave ubuntu:www-data ownership of /var/www/html, and turned on password SSH login for the Azure DevOps service connection. The server password is encrypted with Ansible Vault. Checks confirmed HTTP 200 from the server and password login enabled.
3. Agent registration (Ansible + Vault): install_agent.yml installed agent 5.279.0 on the Azure VM (20.110.202.150) and registered it as static-site-agent-1 in StaticSiteAgentPool. It runs as a systemd service, next to the agent that was already there. The PAT is encrypted with Vault and hidden from output with no_log.
4. Secure connection: the ubuntu-nginx-ssh SSH service connection holds the server login details, so the pipeline YAML contains no credentials.
5. Pipeline (azure-pipelines.yml): runs on a push to any branch, on StaticSiteAgentPool. It does a clean checkout and prints the run details (name, branch, commit, agent, files). It then copies the site to /var/www/html with CopyFilesOverSSH@0 (clears the folder first, skips .git and azure-pipelines.yml, 20-second timeout). Finally it checks the server with SSH@0: lists the files, confirms index.html exists, requests http://localhost, and stops if any check fails.

Flow: Push → Azure DevOps runs the pipeline → self-hosted agent checks out the code → files copied to EC2 over SSH → checks on the server → site live at http://35.172.100.179.

---

# LinkedIn Requirement

## LinkedIn Post Screenshot

Add a screenshot of your LinkedIn post containing:

* What you automated
* How Terraform, Ansible, and Azure DevOps worked together
* Three to five lines describing the CI/CD workflow
* A screenshot of the successful pipeline or deployed website

![ouput](./screenshots/wk10a2tlink-ss0.png)

## LinkedIn Post URL

**https://lnkd.in/p/g-CNNeXQ**

> Do not expose AWS credentials, SSH private keys, passwords, PATs, or other sensitive information.

---

# Submission Instructions

* Include the short assignment summary.
* Include Screenshots 1–5.
* Include the final website URL.
* Include the LinkedIn post screenshot and URL.
* Confirm that the EC2 instance is running during grading.
* Do not expose a password, SSH private key, passphrase, PAT, AWS credential, account ID, or another secret.

---

# Completion Checklist

* The correct Azure Static Website repository was imported into Azure Repos
* `index.html` is visible in Azure Repos
* Your Full Name was added to the website
* The target EC2 instance was provisioned using Terraform
* A suitable Ubuntu image and EC2 size were selected
* Nginx was configured using Ansible
* SSH login works using the selected authentication method
* The SSH user can write to `/var/www/html`
* TCP ports 22 and 80 are configured correctly
* The self-hosted Azure Pipelines agent is online
* The SSH Service Connection was created successfully
* The YAML trigger includes all branches
* The YAML uses the correct self-hosted agent pool
* The copy and remote-verification tasks completed successfully
* The pipeline status is **Succeeded**
* A new pushed commit triggered the pipeline automatically
* The Azure Static Website loads through the EC2 public IP address
* Your Full Name is visible on the deployed website
* Screenshots 1–5 are included and readable
* The final website URL is included
* The LinkedIn post screenshot and URL are included
* No sensitive information is exposed

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
