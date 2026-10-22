# Assignment 3 — Automate React App Deployment Using Azure DevOps CI/CD

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will create a multi-stage Azure DevOps pipeline that builds, tests, publishes, and deploys a React application to an Ubuntu VM hosted on AWS or Azure. The pipeline will automatically run when changes are committed to `main`, transfer the production build as an artifact, and deploy it through Nginx.

---

# Task 0 — Verify the Starting Environment

## Goal

Confirm that Azure DevOps, the pipeline agent, Terraform, Ansible, and the selected cloud environment are ready.

No submission screenshot is required for this task.

---

# Task 1 — Import and Personalize the React Application

## Goal

Import the React application into Azure Repos and add your Full Name and the current date.

## Evidence

### Screenshot 1 — Imported React Project in Azure Repos

Add a screenshot of Azure Repos showing:

* Imported React project
* Repository name
* `main` branch
* Project files

![ouput](./screenshots/wk10a3t1-ss1.png)

---

# Task 2 — Provision and Configure the Target VM

## Goal

Provision an Ubuntu VM using Terraform and configure Nginx, React SPA routing, SSH access, and deployment permissions using Ansible.

No separate submission screenshot is required for this task.

---

# Task 3 — Create or Update the SSH Service Connection

## Goal

Create or update an Azure DevOps SSH Service Connection that allows the pipeline to connect securely to the target VM.

No separate submission screenshot is required for this task.

> Do not include the VM password, SSH private key, token, or another secret in the submission.

---

# Task 4 — Author the Multi-Stage Azure Pipeline

## Goal

Create an Azure Pipeline containing Build, Test, Publish, and Deploy stages with an automatic trigger for commits to `main`.

## Evidence

### Screenshot 2 — Multi-Stage Pipeline YAML

Add a screenshot of the Azure Pipeline YAML open in the editor showing:

* Trigger
* Build stage
* Test stage
* Publish stage
* Deploy stage

![ouput](./screenshots/wk10a3t4-ss2.png)
![ouput](./screenshots/wk10a3t4-ss2a.png)
![ouput](./screenshots/wk10a3t4-ss2b.png)
![ouput](./screenshots/wk10a3t4-ss2c.png)
![ouput](./screenshots/wk10a3t4-ss2d.png)

> Do not expose passwords, private keys, tokens, or cloud credentials.

---

# Task 5 — Run the Pipeline and Resolve Configuration Issues

## Goal

Complete a successful end-to-end pipeline run containing all four stages.

## Evidence

### Screenshot 3 — Successful Multi-Stage Pipeline Run

Add a screenshot of one Azure DevOps pipeline run showing all four stages succeeded:

* Build
* Test
* Publish
* Deploy

![ouput](./screenshots/wk10a3t5-ss3.png)
![ouput](./screenshots/wk10a3t5-ss3a.png)

---

# Task 6 — Verify the Deployment on the VM

## Goal

Confirm that the pipeline deployed the production-ready React files to the correct Nginx web root.

## Evidence

### Screenshot 4 — Post-Deployment Contents of /var/www/html

Add a screenshot of the pipeline SSH verification log or VM terminal showing the post-deployment contents of:

`/var/www/html`

![ouput](./screenshots/wk10a3t6-ss4.png)


---

# Task 7 — Verify the Website and Automatic Trigger

## Goal

Confirm that the React application is accessible and that a commit to `main` automatically triggers the CI/CD pipeline.

## Evidence

### Screenshot 5 — Deployed React Application

Add a browser screenshot showing:

* Deployed React application
* VM public IP address in the browser address bar
* Your Full Name
* Deployment date

![ouput](./screenshots/wk10a3t7-ss5.png)


## Final Application URL

**http://54.236.34.158/**

---

# CI/CD Workflow Summary

Write a short explanation of the CI/CD workflow you created.

- This project uses an Azure DevOps pipeline to build, test and deploy a React application to an Nginx web server on AWS EC2. Every commit to the main branch of the Azure Repos repository starts the pipeline automatically, with no manual steps.

Before the pipeline, I used Ansible to prepare both machines:
- Web server (EC2): site.yml installed and set up Nginx so that React page routes fall back to index.html, cleared the old website files from /var/www/html, and turned on SSH password login so the pipeline can connect.
- Self-hosted agent (Azure VM): install_node.yml installed Node.js and npm.

The pipeline runs four stages on the self-hosted agent in StaticSiteAgentPool. Each stage runs only if the one before it succeeds:

1. Build: checks out the code, installs Node.js 20 and the dependencies, runs npm run build, confirms that build/index.html exists, and saves the build output as the reactBuild artifact.
2. Test: installs the dependencies again and runs the unit tests in CI mode. If a test fails, the pipeline stops before anything is deployed.
3. Publish: downloads reactBuild, confirms that index.html and the static/ assets are present, and publishes the result as the deploymentPackage artifact.
4. Deploy: copies deploymentPackage to /var/www/html over SSH through the ubuntu-nginx-ssh service connection, clearing out the old files first. It then connects to the server to confirm that index.html is in place, Nginx is running, and the site returns HTTP 200.

Artifacts carry the build output between stages, because each job may start with an empty working folder. The service connection keeps the server password out of the YAML file, and the password in the Ansible files is stored encrypted with Ansible Vault. Only the compiled static files reach the server; the source code and node_modules do not. The result is a repeatable workflow: commit to main, and the change is built, tested, deployed and verified on the live site.


---

# LinkedIn Requirement

## Evidence

### Screenshot 6 — LinkedIn Post

Add a screenshot of your LinkedIn post showing:

* Post text
* At least one image or link

![ouput](./screenshots/wk10a3tlink-ss0.png)

## LinkedIn Post URL

**https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-devops-azuredevops-ugcPost-7517178064032665602-FLPt**

> Do not expose VM passwords, tokens, private keys, cloud credentials, or other sensitive information.

---

# Submission Instructions

* Complete all tasks in sequence.
* Include the short CI/CD workflow summary.
* Include Screenshots 1–6.
* Include the final application URL.
* Include the public LinkedIn post URL.
* Confirm that all screenshots are readable and show the required context.
* Do not expose passwords, PATs, private keys, cloud credentials, subscription IDs, account IDs, or other secrets.
* Follow the Assignment Submission Guidelines.

---

# Completion Checklist

* [x] All tasks were completed in sequence
* [x] The correct React repository was imported into Azure Repos
* [x] Your Full Name and date were added to the application
* [x] The pipeline YAML was authored and committed to the repository
* [x] Commits to `main` trigger the pipeline automatically
* [x] The pipeline contains Build, Test, Publish, and Deploy stages
* [x] All four stages succeeded in the same pipeline run
* [x] The production build moved between stages as a pipeline artifact
* [x] The Deploy stage used the SSH Service Connection
* [x] No password or secret is stored in the YAML
* [x] `index.html` is directly inside `/var/www/html`
* [x] Raw React source code was not deployed to the Nginx web root
* [x] `node_modules/` was not deployed to the Nginx web root
* [x] Nginx is active
* [x] The application opens through the VM public IP address
* [x] Your Full Name and date are visible in the browser screenshot
* [x] Screenshots 1–6 are included and readable
* [x] No password, token, private key, account ID, or other secret is visible
* [x] The final application URL is included
* [x] The LinkedIn post is published
* [x] The LinkedIn post URL is included

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
