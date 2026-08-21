# Assignment 5 — Deploy a Highly Available Two-Tier Application on AWS (VPC + ALB + ASG + Multi-AZ RDS)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will design and deploy a highly available two-tier web application on AWS: highly available networking across two Availability Zones, an Application Load Balancer, an Auto Scaling Group for the web tier, and a private Multi-AZ RDS database. You must prove high availability with real failure tests.

---

# Task 1 — Create HA Networking (VPC + 4 Subnets + IGW + NAT + Route Tables)

## Goal

Build a VPC (10.0.0.0/16) with two public and two private subnets across two Availability Zones, an Internet Gateway, a NAT Gateway, and the matching public/private route tables.

### Evidence

#### Screenshot 1 — VPC details showing CIDR 10.0.0.0/16

![ouput](./screenshots/wk6a5t1-ss1.png)

---

#### Screenshot 2 — Subnets list showing four subnets and their Availability Zones

![ouput](./screenshots/wk6a5t1-ss2.png)

![ouput](./screenshots/wk6a5t1-ss2a.png)
---

#### Screenshot 3 — Public route table showing the Internet Gateway route and both public-subnet associations

![ouput](./screenshots/wk6a5t1-ss3.png)

![ouput](./screenshots/wk6a5t1-ss3a.png)

---

#### Screenshot 4 — Private route table showing the NAT Gateway route and both private-subnet associations

![ouput](./screenshots/wk6a5t1-ss4.png)

![ouput](./screenshots/wk6a5t1-ss4a.png)

---

#### Screenshot 5 — NAT Gateway status showing Available and the Elastic IP

![ouput](./screenshots/wk6a5t1-ss5.png)

---

# Task 2 — Create Security Groups (ALB, EC2, RDS) with Least Privilege

## Goal

Create `ha-alb-sg` (HTTP public), `ha-web-sg` (HTTP only from `ha-alb-sg`, SSH from your IP), and `ha-db-sg` (database port only from `ha-web-sg`).

### Evidence

#### Screenshot 6 — ALB Security Group inbound rules

![ouput](./screenshots/wk6a5t2-ss6.png)

---

#### Screenshot 7 — EC2 Security Group inbound rules showing the ALB Security Group reference and SSH from your IP

![ouput](./screenshots/wk6a5t2-ss7.png)

---

#### Screenshot 8 — RDS Security Group inbound rule showing the database port allowed only from the EC2 Security Group

![ouput](./screenshots/wk6a5t2-ss8.png)

---

# Task 3 — Deploy Database Tier (RDS Multi-AZ in Private Subnets)

## Goal

Launch a private, Multi-AZ RDS database (MySQL or PostgreSQL) using the private DB Subnet Group and `ha-db-sg`.

### Evidence

#### Screenshot 9 — RDS summary showing Multi-AZ = Yes and Publicly accessible = No

![ouput](./screenshots/wk6a5t3-ss9.png)

![ouput](./screenshots/wk6a5t3-ss9a.png)

---

#### Screenshot 10 — RDS connectivity section showing the DB Subnet Group and Security Group

![ouput](./screenshots/wk6a5t3-ss10.png)

---

# Task 4 — Build a Launch Template (User Data Installs App + Connects to DB)

## Goal

Create a Launch Template whose user data installs the web-server runtime, deploys the application, configures the database connection, and starts the required services.

### Evidence

#### Screenshot 11 — Launch Template details showing that user data exists, including a visible snippet

![ouput](./screenshots/wk6a5t4-ss11.png)

![ouput](./screenshots/wk6a5t4-ss11a.png)

---

#### Screenshot 12 — A running instance created from the template showing that the application responds on port 80 through a local test or browser using its public IP

![ouput](./screenshots/wk6a5t4-ss12.png)

![ouput](./screenshots/wk6a5t4-ss12a.png)

---

# Task 5 — Create an Application Load Balancer (ALB) Across 2 Public Subnets

## Goal

Create an internet-facing ALB across both public subnets with an HTTP listener and a healthy instance target group.

### Evidence

#### Screenshot 13 — ALB details showing two public subnets in two Availability Zones

![ouput](./screenshots/wk6a5t5-ss13.png)

---

#### Screenshot 14 — Target group showing at least one healthy target

![ouput](./screenshots/wk6a5t5-ss14.png)

![ouput](./screenshots/wk6a5t5-ss14a.png)

---

# Task 6 — Create Auto Scaling Group (ASG) in 2 Public Subnets

## Goal

Create an Auto Scaling Group from the Launch Template across both public subnets, with desired capacity 2, minimum 2, and maximum 4, registered to the ALB target group.

### Evidence

#### Screenshot 15 — Auto Scaling Group showing desired, minimum, and maximum capacity and the selected subnet Availability Zones

![ouput](./screenshots/wk6a5t6-ss15.png)

---

#### Screenshot 16 — EC2 instances list showing two running instances in different Availability Zones

![ouput](./screenshots/wk6a5t6-ss16.png)

---

# Task 7 — Configure App to Use RDS + Validate Read/Write

## Goal

Confirm the application communicates with the RDS database through the ALB DNS name with at least one read and one write operation.

### Evidence

#### Screenshot 17 — Browser showing the application loaded through the ALB DNS name with the URL visible

![ouput](./screenshots/wk6a5t7-ss17.png)

![ouput](./screenshots/wk6a5t7-ss17a.png)

---

#### Screenshot 18 — Proof of a database write through a UI message or database query output

![ouput](./screenshots/wk6a5t7-ss18.png)

![ouput](./screenshots/wk6a5t7-ss18a.png)

![ouput](./screenshots/wk6a5t7-ss18b.png)

![ouput](./screenshots/wk6a5t7-ss18c.png)

---

# Task 8 — High Availability Tests (Must Do Both)

## Goal

Test A: terminate one web instance and confirm the Auto Scaling Group replaces it automatically without interrupting the ALB.

Test B: simulate an Availability Zone impact (stop, detach, or reduce desired capacity in one AZ) and confirm the application stays available.

### Evidence

#### Screenshot 19 — EC2 showing the terminated instance and the newly launched instance; timestamps are helpful

![ouput](./screenshots/wk6a5t8-ss19.png)

![ouput](./screenshots/wk6a5t8-ss19a.png)
---

#### Screenshot 20 — Target group showing healthy targets after replacement

![ouput](./screenshots/wk6a5t8-ss20.png)

---

#### Screenshot 21 — Evidence that an instance was removed, detached, placed in Standby, or stopped in one Availability Zone

![ouput](./screenshots/wk6a5t8-ss21.png)

---

#### Screenshot 22 — Browser showing that the ALB DNS endpoint still works during the change

![ouput](./screenshots/wk6a5t8-ss22.png)

![ouput](./screenshots/wk6a5t8-ss22a.png)

---

# Task 9 — Architecture and Test-Results Summary

## Goal

Summarize the VPC/subnet layout, the ALB and Auto Scaling Group setup, the private Multi-AZ RDS setup, and the results of both high-availability tests.

### Evidence

#### Screenshot 23 — A simple architecture diagram, which may be hand-drawn, or an AWS console overview showing the components

![ouput](./screenshots/wk6a5t9-ss23.png)

---

### Notes

**Summarize the VPC and subnets across the two Availability Zones.**
VPC and Subnets:
I built the environment inside a dedicated ha-vpc using a multi-AZ design. The web tier is distributed across public subnets in two Availability Zones, allowing EC2 instances to run independently across AZs. The database tier uses separate private subnets across the two AZs, keeping RDS isolated from direct internet access while still allowing the web servers to communicate with it securely.

**Summarize the ALB and Auto Scaling Group setup.**
Application Load Balancer (ALB) and Auto Scaling:
An internet-facing Application Load Balancer sits in front of the WordPress web tier and distributes HTTP traffic across the EC2 instances. The instances are managed through an Auto Scaling Group using my launch template, which automatically installs and configures Apache, PHP, and WordPress. Target group health checks ensure that traffic is sent only to healthy instances, while the Auto Scaling Group can replace an unhealthy or terminated instance automatically.

**Summarize the private Multi-AZ RDS setup.**
Private Multi-AZ RDS:
The WordPress database runs on Amazon RDS for MySQL in private subnets using a Multi-AZ configuration. The database is not publicly accessible, and its security group permits MySQL traffic on port 3306 only from the web tier. I verified the connection directly from an EC2 instance and confirmed that WordPress could read and write data to the shared appdb database. Because all web instances use the same RDS database, application data remains available even when individual EC2 instances are replaced.

**Summarize the results of both high-availability tests.**
High-Availability Test Results:
I performed two failure tests to validate the architecture. For the web-tier test, an EC2 instance was removed/stopped and the environment continued serving the application through the ALB. The target group returned to healthy status after the replacement instance came online, demonstrating that the load balancer and Auto Scaling Group could maintain application availability during an instance failure.

For the database-tier test, the Multi-AZ RDS configuration provided database redundancy across Availability Zones. The application continued using the RDS endpoint rather than depending on a specific database server. Together, the tests demonstrated the main objective of the architecture: individual infrastructure components can fail or be replaced without requiring the entire application to go offline.

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post about the high-availability build, including the ALB URL (or a redacted screenshot), three to five lines on what you built and how you tested high availability, and one proof screenshot.

## Evidence

#### LinkedIn Post URL

**[Ginny Ibe](https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-devops-agenticai-ugcPost-7495303848438214656-g-H4/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGTqulMBvpSBQMnxbzFBrJkA0C9nlWM_uqM)**

---

#### Screenshot of LinkedIn post

![ouput](./screenshots/wk6a5t0link-ss.png)

---

# Submission Instructions

- Add all required screenshots in your submission
- Do not expose passwords, connection strings, private keys, or account IDs

---

# Completion Checklist

- [x] Task 1: VPC, four subnets, IGW, NAT Gateway, and route tables created (Screenshots 1–5)
- [x] Task 2: Least-privilege ALB, EC2, and RDS security groups created (Screenshots 6–8)
- [x] Task 3: Private Multi-AZ RDS created (Screenshots 9–10)
- [x] Task 4: Self-configuring Launch Template created and tested (Screenshots 11–12)
- [x] Task 5: ALB created across both public subnets (Screenshots 13–14)
- [x] Task 6: Auto Scaling Group running two instances across two AZs (Screenshots 15–16)
- [x] Task 7: Application verified through the ALB with a database read and write (Screenshots 17–18)
- [x] Task 8: Both high-availability tests completed (Screenshots 19–22)
- [x] Task 9: Architecture and test-results summary completed (Screenshot 23 & Notes)
- [x] LinkedIn post published and URL submitted
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

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*