# Assignment 5 — Deploy EpicBook Web App on Azure VM with Azure Database for MySQL

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook web application on Azure using an Ubuntu Virtual Machine to host the frontend and backend, and Azure Database for MySQL Flexible Server (private access) to store user and product data. You will build the network, provision the resources, deploy the application, and prove that the complete user flow works through the VM's public IP.

---

# Task 1 — Create Network Infrastructure

## Goal

Create a Resource Group and VNet (`10.0.0.0/16`) with `public-subnet` (`10.0.1.0/24`) for the VM and a delegated `private-db-subnet` (`10.0.2.0/24`) for MySQL. Configure and associate the public and private NSGs with the required HTTP, SSH, and MySQL rules.

## Evidence

#### Screenshot 1 — Virtual Network configuration showing the `10.0.0.0/16` address space and both subnets

![ouput](./screenshots/wk7a5t1-ss1.png)

![ouput](./screenshots/wk7a5t1-ss1a.png)

---

#### Screenshot 2 — Public and private NSG inbound rules showing HTTP 80, SSH 22, and the MySQL 3306 rule with source `10.0.1.0/24`

Public NSG
![ouput](./screenshots/wk7a5t1-ss2.png)

Private NSG
![ouput](./screenshots/wk7a5t1-ss2a.png)

---

#### Screenshot 3 — Public IP and Network Interface association for the Virtual Machine

![ouput](./screenshots/wk7a5t1-ss3.png)

---

# Task 2 — Provision Azure Virtual Machine

## Goal

Deploy a supported Ubuntu LTS VM in `public-subnet`, allowing Azure to create its NIC and Public IP. Connect using SSH and install Node.js, npm, Nginx, Git, MySQL Client, and curl.

## Evidence

#### Screenshot 3 — VM Network Interface configuration showing its association with the public subnet and Public IP

![ouput](./screenshots/wk7a5t2-ss4.png)

---

#### Screenshot 4 — Virtual Machine overview showing the Ubuntu VM, VM size, Public IP address, and subnet

![ouput](./screenshots/wk7a5t2-ss5.png)

---

#### Screenshot 5 — Terminal showing successful installation or version checks for Node.js, npm, Nginx, Git, and MySQL Client

Add your screenshot here.

---

# Task 3 — Configure Azure Database for MySQL and Prepare the EpicBook Database

## Goal

Create Azure Database for MySQL Flexible Server using Private access (VNet Integration) in the delegated database subnet. Clone the EpicBook repository to the VM, create `bookstore`, import the schema and seed data, and verify the database contents.

## Evidence

#### Screenshot 6 — MySQL Flexible Server overview showing the server running and configured with private access

![ouput](./screenshots/wk7a5t3-ss6.png)

---

#### Screenshot 7 — MySQL networking configuration showing the server connected to the private database subnet

![ouput](./screenshots/wk7a5t3-ss7.png)

---

#### Screenshot 8 — VM terminal showing the `bookstore` database tables and seeded author and book records

![ouput](./screenshots/wk7a5t3-ss8.png)

![ouput](./screenshots/wk7a5t3-ss8a.png)

---

# Task 4 — Deploy and Configure the EpicBook Application

## Goal

Install the EpicBook dependencies, configure `config/config.json` with the Azure MySQL connection details, and configure Nginx to proxy HTTP traffic from port 80 to the Node.js application on port 8080.

## Evidence

#### Screenshot 9 — Terminal showing the EpicBook repository, successful dependency installation, and the `node_modules` folder

![ouput](./screenshots/wk7a5t4-ss9.png)

---

#### Screenshot 10 — Networking configuration showing the private subnet and restricted access
![ouput](./screenshots/wk7a5t4-ss10.png)

---

#### Screenshot 11 — Terminal showing the EpicBook application running and listening on port 8080

![ouput](./screenshots/wk7a5t4-ss11.png)

---

# Task 5 — Test End-to-End Functionality

## Goal

Verify that EpicBook loads through the VM’s public IP, displays products, supports cart operations and checkout, and records the corresponding changes in Azure Database for MySQL.

## Evidence

#### Screenshot 12 — Browser showing EpicBook with the Virtual Machine public IP visible in the address bar

![ouput](./screenshots/wk7a5t5-ss12.png)

![ouput](./screenshots/wk7a5t5-ss12a.png)

---

#### Screenshot 13 — Proof of a successful cart or order action together with the corresponding database verification

![ouput](./screenshots/wk7a5t5-ss13.png)

![ouput](./screenshots/wk7a5t5-ss13a.png)

---

#### Public IP URL

Paste your working website URL here:

`http://<VM-PUBLIC-IP>`

---

# Configure PM2 for Application Persistence

## Goal

Run EpicBook in the background using PM2, configure startup after a VM reboot, save the process list, and verify that the application remains accessible through Nginx.

## Evidence

#### Screenshot 14 — Terminal showing the `epicbook` process with status `online` in `pm2 list`

Add your screenshot here.

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post with an EpicBook application screenshot, your website URL, and a caption explaining what you deployed, what you learned, a challenge you resolved, and how you verified application actions in the database.

Include **#DMIByPravinMishra #Azure #DevOps #CloudComputing**. Add the credit note and tags for your track as specified in the assignment guidelines.

## Evidence

#### LinkedIn Post URL

Paste your published LinkedIn post URL here:

**http://4.154.19.47/**

---

#### Screenshot 15 — Published LinkedIn post showing your caption, website URL, and EpicBook application screenshot

Add your screenshot here.

---

# Submission Instructions

- Include your full name and Screenshots **1–15** under the corresponding evidence headings.
- Include the working **Virtual Machine public IP URL** and published **LinkedIn post URL**.
- Add brief notes describing your network, VM, application, and database configuration.
- Include proof of product viewing, cart operations, and order placement.
- Ensure screenshots are readable and show your own environment.
- Do not expose database passwords, private keys, connection strings containing credentials, subscription IDs, or other sensitive information.
- Submit your work using the method specified in the assignment guidelines.

---

# Completion Checklist

- [x] Task 1: Network foundation created with public/private subnets and NSGs (Screenshots 1–3)
- [x] Task 2: VM provisioned and required software installed (Screenshots 4–5)
- [x] Task 3: EpicBook frontend and backend deployed (Screenshots 6–8)
- [x] Task 4: Private Azure Database for MySQL created and data imported (Screenshots 9–11)
- [x] Task 5: End-to-end functionality validated (Screenshots 12–13, Public IP URL)
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
