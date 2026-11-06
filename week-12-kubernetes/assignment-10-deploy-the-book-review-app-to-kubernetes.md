# Assignment 10 — Capstone: Deploy the Book Review App to Kubernetes

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

Deploy the Book Review App as a production-style three-tier application using Kubernetes manifests or Helm charts, with an accessible frontend, an internal backend, and a managed or persistent MySQL database.

---

## Submission Details

**Full Name:** Ginny Ibe 

**Kubernetes Platform:**  Amazon EKS (Kubernetes v1.37), region eu-north-1. Cluster eks-lb-lab with one managed node (c7i-flex.large, Amazon Linux 2023), created with Terraform. Add-ons: Amazon EBS CSI driver (IRSA) and Amazon VPC CNI with NetworkPolicy enforcement enabled.

**Namespace:** book-review

**Frontend URL or IP:** http://a7f7ba34d042a40e1a28d25537be611f-733616926.eu-north-1.elb.amazonaws.com

**Access Method:** Public LoadBalancer, Ingress, or documented local access.

**GitHub Manifests or Helm Chart URL:** https://github.com/Ginny-Ibe/book-review-k8s (YAML manifests, no Helm)

**Architecture Diagram:** https://github.com/Ginny-Ibe/book-review-k8s/blob/main/architecture.png (also attached as Screenshot 3)

---

# Task 1 — Prepare the Kubernetes Cluster and Project

## Goal

Prepare the cluster, application source, container images, and tooling needed for the deployment.

### Evidence

#### Screenshot 1 — Active Kubernetes context and Ready nodes

![ouput](./screenshots/wk12a10t1-ss1.png)

---

#### Screenshot 2 — If using Helm: Output of `helm version`

Required only if you choose Helm. Skip this screenshot if using YAML manifests.

Not applicable — using YAML manifests.

---

# Task 2 — Design the Three-Tier Kubernetes Architecture

## Goal

Document the workloads, networking, configuration, storage, and permitted communication paths before deployment.

### Evidence

#### Screenshot 3 — Completed Kubernetes architecture diagram

![ouput](./screenshots/wk12a10t2-ss3.png)

---

# Task 3 — Create ConfigMaps and Secrets

## Goal

Provide application configuration and credentials without embedding sensitive values in images or committing them to Git.

### Evidence

#### Screenshot 4 — ConfigMap configuration or describe output showing non-sensitive values

![ouput](./screenshots/wk12a10t3-ss4.png)

---

#### Screenshot 5 — Secret references or describe output showing names and keys without sensitive values

![ouput](./screenshots/wk12a10t3-ss5.png)

---

# Task 4 — Deploy and Configure the MySQL Data Tier

## Goal

Provide persistent MySQL storage and allow the backend to connect without exposing the database publicly.

### Evidence

#### Screenshot 6 — Managed MySQL status or MySQL StatefulSet and PVC status

![ouput](./screenshots/wk12a10t4-ss6.png)

---

#### Screenshot 7 — Successful database connectivity or schema verification with credentials hidden

![ouput](./screenshots/wk12a10t4-ss7.png)
![ouput](./screenshots/wk12a10t4-ss7a.png)
![ouput](./screenshots/wk12a10t4-ss7b.png)

---

# Task 5 — Deploy the Backend Application Tier

## Goal

Run one or two Node.js/Express backend replicas on port 3001 and expose them through an internal ClusterIP Service.

### Evidence

#### Screenshot 8 — Backend Deployment, ready Pods, and internal Service

![ouput](./screenshots/wk12a10t5-ss8.png)

---

#### Screenshot 9 — Backend startup logs and database connectivity evidence

If the application does not log successful database connections, provide a successful database-backed request alongside the startup logs.

![ouput](./screenshots/wk12a10t5-ss9.png)

---

# Task 6 — Deploy the Frontend and Configure Nginx

## Goal

Run the Next.js frontend behind Nginx on port 80 and proxy API requests to the internal backend Service.

### Evidence

#### Screenshot 10 — Frontend Deployment and Ready Pods

![ouput](./screenshots/wk12a10t6-ss10.png)

---

#### Screenshot 11 — Nginx reverse-proxy configuration showing the internal backend Service name and port 3001

![ouput](./screenshots/wk12a10t6-ss11.png)
![ouput](./screenshots/wk12a10t6-ss11a.png)

---

# Task 7 — Configure External and Internal Networking

## Goal

Expose the frontend and enforce the intended communication path between application tiers.

### Evidence

#### Screenshot 12 — Frontend LoadBalancer or Ingress configuration and assigned endpoint, or documented local access method

![ouput](./screenshots/wk12a10t7-ss12.png)

---

#### Screenshot 13 — Internal backend Service and NetworkPolicy or equivalent restriction, with access-test evidence

![ouput](./screenshots/wk12a10t7-ss13.png)

---

# Task 8 — Validate the Application End to End

## Goal

Prove that the frontend, backend, and MySQL database work together and collect operational evidence.

### Evidence

#### Screenshot 14 — `kubectl get all` output for the book-review namespace

![ouput](./screenshots/wk12a10t8-ss14.png)

---

#### Screenshot 15 — Functional Book Review App in the browser with the URL visible, including evidence of a successful user flow

![ouput](./screenshots/wk12a10t8-ss15.png)
![ouput](./screenshots/wk12a10t8-ss15a.png)
![ouput](./screenshots/wk12a10t8-ss15b.png)
![ouput](./screenshots/wk12a10t8-ss15c.png)

---

#### Screenshot 16 — Frontend or Nginx container logs

![ouput](./screenshots/wk12a10t8-ss16.png)

---

#### Screenshot 17 — Backend logs and evidence of a successful database-backed operation

![ouput](./screenshots/wk12a10t8-ss17.png)

---

#### Screenshot 18 — Optional: Kubernetes dashboard

![ouput](./screenshots/wk12a10t8-ss18.png)
![ouput](./screenshots/wk12a10t8-ss18a.png)

Multiple images may be included under a screenshot number when needed to demonstrate the complete user flow.

---

# Task 9 — Optional: Implement Production Improvements

## Goal

Add selected improvements without breaking the required three-tier architecture.

### Evidence

#### Screenshot 19 — Optional: Evidence of selected Helm, HPA, probe, or TLS improvements

![ouput](./screenshots/wk12a10t9-ss19.png)

---

# Task 10 — Publish Your Book Review App Deployment on LinkedIn

## Goal

Explain your three-tier architecture, demonstrate the working application, and share what you learned.

### Evidence

#### Screenshot 20 — Published LinkedIn post about your Book Review App deployment on Kubernetes

![ouput](./screenshots/wk12a10t10link-ss20.png)

### LinkedIn Post URL
**https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-kubernetes-devops-ugcPost-7524337835814981632-6fqZ/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGTqulMBvpSBQMnxbzFBrJkA0C9nlWM_uqM**

---

# Final Summary

## What Worked

Describe the application components and user flows you successfully deployed and tested.

I deployed the Book Review App as a three-tier application in the book-review namespace on Amazon EKS (eks-lb-lab, eu-north-1, one c7i-flex.large node, created with Terraform):

- Data tier. MySQL 8.0 runs as the StatefulSet mysql behind a headless Service on port 3306. Its data lives on a 5Gi PVC (mysql-data-mysql-0) on the ebs-gp3 StorageClass, provisioned by the EBS CSI driver. A row survived deleting and recreating mysql-0, which proves the data persists.
- Application tier. The Node.js/Express backend (ginnyibe/book-review-backend:v1) runs on port 3001 behind the internal ClusterIP Service backend-service. Its logs show connected successfully with SSL!, the schema being created, and sample users, books and reviews being seeded. /api/books returned the 3 books from MySQL.
- Presentation tier. The frontend Deployment has 2 replicas, each Pod running the Next.js production build (ginnyibe/book-review-frontend:v1, port 3000) plus an Nginx sidecar on port 80. The Nginx config, kept in a ConfigMap, sends / to Next.js and /api/ (and /api/api/) to backend-service:3001.
- External access. The LoadBalancer Service frontend-service is the only public entry point, at a7f7ba34d042a40e1a28d25537be611f-733616926.eu-north-1.elb.amazonaws.com.
- Network security. NetworkPolicies allow only frontend → backend on 3001 and only backend → MySQL on 3306. Tests from an unlabelled Pod to both the backend and MySQL timed out (curl exit code: 28), while the frontend and public paths returned books.
- User flows tested in the browser: viewing the book list, opening a book's details, registering a user, logging in, and posting a review. The new user and the review Kubernetes test review were confirmed in the Users and Reviews tables.

## Issues and Fixes

Describe the main issues encountered, their causes, and how you resolved them.

| Issue | Cause | Fix |
|---|---|---|
| **`kubectl: no such host`** | The kubeconfig still pointed to the destroyed Assignment 09 cluster. | Deleted the stale context, cluster, and user entries, then ran `aws eks update-kubeconfig --alias eks-lb-lab` with the required cluster name and region. |
| **Risk of exposing `.env` in the Docker image** | `backend/.env` was committed to the repository, and the Dockerfile used `COPY . .`, potentially including sensitive credentials. | Added `backend/.dockerignore` and verified that `.env` was absent from `/app` inside the image using `ls -la /app`. |
| **Frontend Dockerfile ran `npm run dev`** | The repository's original Dockerfile was intended for development rather than production. | Created `Dockerfile.k8s` using `npm ci`, `npm run build`, and `npm start`, with `NEXT_PUBLIC_API_URL=/api` supplied as a build argument. |
| **Home page requested `/api/api/books`** | `page.js` appended `/api/books` to the API URL, while `api.js` used the URL without modification. | Added an Nginx `location /api/api/` rule to forward those requests to the backend's `/api/` endpoint. |
| **Backend defaulted to port 5000** | The application used `PORT \|\| 5000` as its fallback configuration. | Set `PORT=3001` in the backend ConfigMap. |
| **PVC remained Pending** | The cluster lacked the required EBS CSI provisioner and had only the older in-tree `gp2` provisioner. | Installed the Amazon EBS CSI driver add-on using an IRSA role (`AmazonEKS_EBS_CSI_DriverRole`) and created the `ebs-gp3` StorageClass. |
| **NetworkPolicies were not enforced** | The VPC CNI was self-managed with `enableNetworkPolicy=false`. The initial `update-addon` command failed because no managed add-on existed. | Created the managed `vpc-cni` add-on with `enableNetworkPolicy: true` and verified that the setting changed to `true`. |
| **CORS could block browser requests** | `ALLOWED_ORIGINS` contained a placeholder before the ELB hostname became available. | Updated `ALLOWED_ORIGINS` with the actual ELB URL using `sed`, reapplied the ConfigMap, and restarted the backend deployment. |
| **Errors in the solution documentation** | The instructions contained malformed commands, an unset `ROLE_ARN`, incorrect `sed` syntax, an invalid NetworkPolicy denial test, contradictory replica counts, incorrect file references, and another cluster's ELB hostname. | Corrected the commands, initialized `ROLE_ARN`, tested denied traffic using a Pod without the permitted label, configured one backend and two frontend replicas, and used the actual ELB hostname. |
| **Credentials could appear in screenshots** | Step 10b of the documentation displayed the database password in the terminal. | Passed credentials directly from the Kubernetes Secret into the test Pod through `MYSQL_PWD`. Used server-side apply and verified that no plaintext credentials appeared in annotations. |
| **Test Pods remained after execution (`AlreadyExists`)** | `kubectl run --rm -i` lost its attach connection, preventing automatic cleanup. | Retrieved logs from leftover Pods, deleted them, and adopted a reliable `run → wait → logs → delete` testing workflow. |


## Tools and Sources Used

List the documentation and tools that helped you complete the assignment.

Assignment material: DMI Week 12 Assignment 10 brief and solution guide (Pravin Mishra), and the application repo github.com/pravinmishraaws/book-review-app
- Infrastructure: Terraform with terraform-aws-modules/vpc and terraform-aws-modules/eks, AWS CLI v2, eksctl
- Kubernetes: kubectl, the Kubernetes docs (StatefulSets, Services, ConfigMaps, Secrets, NetworkPolicy, probes)
- AWS docs: Amazon EKS user guide for the EBS CSI driver add-on, IRSA, VPC CNI network policy and LoadBalancer Services
- Images and testing: Docker Desktop and Docker Hub; Nginx and Next.js documentation; curlimages/curl, mysql:8.0 and busybox test Pods; browser DevTools (Network tab)

## Optional Improvements

Describe any optional improvements implemented, or write “Not attempted.”

- Probes: added liveness probes to the backend and both frontend containers, and a readiness probe for Next.js. Nginx uses a TCP liveness check, so a slow Next.js can't get a healthy Nginx restarted. The rollout caused no downtime: the LoadBalancer kept returning 200 OK.
- Dashboard: used the Amazon EKS console's Kubernetes resources view (Screenshot 18).
- Not attempted: Helm, HPA, TLS.

## Cleanup or Reuse Plan

State which resources were removed or why the environment is being retained.

The environment is kept running only until the evidence and LinkedIn post are complete. It's then removed in this order, so no AWS resources are left behind:
  1. kubectl delete svc frontend-service -n book-review: removes the AWS load balancer and its security group, which would otherwise block the VPC from being deleted.
  2. kubectl delete namespace book-review: removes all workloads and the PVC. Because reclaimPolicy: Delete is set, the 5Gi EBS volume is deleted too.
 3. eksctl delete iamserviceaccount --cluster eks-lb-lab --namespace kube-system --name ebs-csi-controller-sa: removes the IRSA role and its CloudFormation stack, which Terraform doesn't manage.
  4. terraform destroy in ~/k8s-labs/terraform/eks-lb-lab: removes the EKS cluster, node group and VPC. 
  5. Check that no ELBs, EBS volumes or CloudFormation stacks remain in eu-north-1.

The manifests (k8s/, including 02-secret.example.yaml but not secrets-local/) and the Docker Hub images are kept, so the stack can be rebuilt.

---

# Submission Instructions

- Include your Kubernetes manifests or Helm chart in the linked repository.
- Submit Secret examples containing placeholders only.
- Include Screenshot 1, Screenshots 3–17, and Screenshot 20.
- Include Screenshot 2 only if using Helm.
- Screenshots 18–19 are optional.
- Keep screenshot numbering consistent with the guidelines.
- Use clear, readable screenshots showing relevant configuration, commands, and outputs.
- Make your full name visible in required screenshots.
- Complete the submission details and Final Summary.
- Include your published LinkedIn post URL.
- Capture required evidence before cleanup.
- Do not expose passwords, tokens, JWT secrets, actual Secret values, kubeconfig contents, cloud credentials, or confidential account information.

---

# Completion Checklist

- [x] Selected and configured a supported Kubernetes platform
- [x] Confirmed the context and Ready nodes (Screenshot 1)
- [x] Created and selected the book-review namespace
- [x] Inspected the application repository
- [x] Prepared images accessible to the cluster
- [x] Confirmed Helm if using charts (Screenshot 2, conditional)
- [x] Created the architecture diagram (Screenshot 3)
- [x] Created ConfigMaps and Secrets (Screenshots 4–5)
- [x] Excluded actual Secret values from Git and screenshots
- [x] Provisioned private managed MySQL or persistent Kubernetes MySQL (Screenshot 6)
- [x] Initialized the required database and schema
- [x] Verified database connectivity or schema (Screenshot 7)
- [x] Deployed one or two backend replicas listening on port 3001
- [x] Created the backend ClusterIP Service (Screenshot 8)
- [x] Verified backend-to-database connectivity (Screenshot 9)
- [x] Deployed one or two frontend replicas behind Nginx (Screenshot 10)
- [x] Configured API proxying through the internal backend Service (Screenshot 11)
- [x] Configured and documented frontend access (Screenshot 12)
- [x] Enforced backend access restrictions (Screenshot 13)
- [x] Confirmed that the backend and database are not publicly exposed
- [x] Captured resource status for book-review (Screenshot 14)
- [x] Tested application functionality and database writes (Screenshots 15 and 17)
- [x] Verified saved data after application restart
- [x] Captured frontend and backend logs (Screenshots 16–17)
- [x] Included optional dashboard or improvement evidence if attempted (Screenshots 18–19)
- [x] Published the LinkedIn post (Screenshot 20)
- [x] Included the LinkedIn post URL
- [x] Documented issues, fixes, and useful sources
- [x] Recorded cleanup status or reuse plans
- [x] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
