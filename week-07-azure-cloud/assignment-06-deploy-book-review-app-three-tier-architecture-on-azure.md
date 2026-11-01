# Assignment 6 — Capstone: Deploy Book Review App (Three-Tier Architecture) on Azure

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

Deploy the Book Review App in a production-style three-tier architecture on Azure, with private application and database tiers, public and internal Load Balancers, protected secrets, monitoring, and restart-recovery testing.

---

# Task 1 — Design the Azure Three-Tier Architecture

## Goal

Create an architecture diagram and implementation plan showing the selected Azure services, subnet ranges, application ports, administration path, and traffic flow.

## Evidence

#### Screenshot 1 — Architecture diagram showing the public entry point, three tiers, network boundaries, and traffic flow

![ouput](./screenshots/wk7a6t1-ss1.png)

---

#### Architecture Notes

![ouput](./screenshots/wk7a6t1-ss2.png)

The Book Review application is designed as a three-tier architecture on Microsoft Azure. The architecture separates the system into the Presentation Tier, Application Tier, and Database Tier. This separation improves security, scalability, and troubleshooting because each tier has a specific responsibility and communicates only through approved network paths.

The entire environment is deployed inside the `Book-Review-RG` Resource Group and uses `Book-Review-VNet` with the address space `10.0.0.0/16`. The VNet is divided into three subnets: `Book-Review-Web-Subnet` (`10.0.1.0/24`), `Book-Review-App-Subnet` (`10.0.2.0/25`), and `Book-Review-DB-Subnet` (`10.0.3.0/26`).

**Presentation Tier**

The Presentation Tier contains `Book-Review-Web-VM`. This VM hosts the Next.js frontend and Nginx reverse proxy. It is placed inside the Web subnet and is the only application VM designed to receive external traffic.

Users access the application through an Azure Public Load Balancer. The Public Load Balancer acts as the main Internet entry point and forwards TCP port 80 traffic to `WebBackendPool`, which contains the Web VM. A TCP port 80 health probe verifies that Nginx is available before traffic is forwarded.

Nginx listens on port 80. Normal frontend requests are forwarded to the local Next.js application running on port 3000, while requests beginning with `/api` are forwarded to the private Internal Load Balancer.

**Application Tier**

The Application Tier contains the private `bookreviewapp` VM inside `Book-Review-App-Subnet`. It runs the Node.js/Express backend and uses PM2 to keep the application running after SSH sessions close or the VM restarts.

The App VM has no public IP address. Communication from the Web Tier passes through `Book-Review-Internal-LB`, which uses the private frontend address `10.0.2.50`.

For the implemented deployment, the backend listens on TCP port `3001`. Therefore, the App NSG, Internal Load Balancer rule, health probe, backend `.env`, and Nginx configuration must all use port `3001`.

The App subnet uses `Book-Review-NAT-Gateway` for outbound Internet access. This allows the App VM to download packages and updates without exposing it directly to incoming Internet traffic.

**Database Tier**

The Database Tier uses Azure Database for MySQL Flexible Server named `book-review-db`. It is privately integrated with `Book-Review-DB-Subnet` and has no public endpoint.

The application database is named `book_review_db`. The backend connects privately to MySQL on TCP port `3306` using SSL. The Database NSG permits MySQL traffic only from the App subnet.

**Selected Azure Services**
The main Azure services used are:
- Azure Resource Group
- Azure Virtual Network
- Azure Subnets
- Network Security Groups
- Route Tables
- Azure NAT Gateway
- Azure Public IP
- Azure Public Load Balancer
- Azure Internal Load Balancer
- Azure Virtual Machines
- Azure Database for MySQL Flexible Server
- Private DNS
- Nginx
- PM2

Final Traffic Flow
Internet → Public Load Balancer → Web VM/Nginx → Internal Load Balancer → App VM/Node.js → Azure MySQL Flexible Server

Ports:
`80 (Web) → 3001 (Application) → 3306 (Database)`

This design keeps the Presentation Tier publicly accessible while keeping the Application and Database tiers private and protected through controlled internal communication.


---

# Task 2 — Create the Azure Network Foundation

## Goal

Create a Resource Group and VNet using `10.0.0.0/16`, with web subnet `10.0.1.0/24`, application subnet `10.0.2.0/25`, and delegated database subnet `10.0.3.0/26`. Associate a NAT Gateway with the web and application subnets and reserve a private frontend IP for the Internal Load Balancer.

## Evidence

#### Screenshot 2 — Resource Group overview showing the network resources created so far

![ouput](./screenshots/wk7a6t2-ss3.png)

---

#### Screenshot 3 — VNet subnet configuration showing all three subnet names, CIDR ranges, and database subnet delegation

![ouput](./screenshots/wk7a6t2-ss4.png)

---

#### Screenshot 4 — NAT Gateway configuration showing its Public IP and associations with the web and application subnets

![ouput](./screenshots/wk7a6t2-ss5.png)

---

# Task 3 — Configure Security and Secret Management

## Goal

Configure NSGs for application, management, and health-probe traffic. Protect database credentials and the JWT signing secret. Create the Web VM with restricted SSH access and use it as the jump host for private App VM administration.

## Evidence

#### Screenshot 5 — Web, application, and database NSG rules showing permitted sources, ports, health-probe access, and applicable deny rules

![ouput](./screenshots/wk7a6t3-ss6.png)

---

#### Screenshot 6 — Key Vault or the selected secret-management configuration, without displaying secret values

![ouput](./screenshots/wk7a6t3-ss7.png)

---

# Task 4 — Deploy the Managed Database Tier

## Goal

Create Azure Database for MySQL Flexible Server using private access in the delegated database subnet. Configure Private DNS, keep secure transport enabled, disable public access, and document backup and availability settings.

## Evidence

#### Screenshot 7 — MySQL Flexible Server networking configuration showing the private subnet and Private DNS configuration

![ouput](./screenshots/wk7a6t4-ss8.png)

---

#### Screenshot 8 — Database backup-retention and availability settings

![ouput](./screenshots/wk7a6t4-ss9.png)

---

#### Database Notes

Record the database service, selected capacity, backup-retention period, and recovery approach. Do not include passwords.

Write your answer here.

---

# Task 5 — Deploy the Business or Application Tier

## Goal

Deploy the backend on one private Ubuntu VM using port **3001**. Connect to Azure MySQL over TLS, create `book_review_db` and the application database user, verify startup schema and sample-data initialization, and configure PM2 for automatic recovery.

## Evidence

#### Screenshot 9 — App VM networking configuration showing placement in the application subnet and no Public IP

![ouput](./screenshots/wk7a6t5-ss10.png)

**CLI equivalent**

![ouput](./screenshots/wk7a6t5-ss10a.png)

---

#### Screenshot 10 — Successful database connection and schema verification from the App VM, without displaying credentials

![ouput](./screenshots/wk7a6t5-ss11.png)


---

#### Screenshot 11 — PM2 showing `book-review-backend` online after reboot

![ouput](./screenshots/wk7a6t5-ss12.png)

---

#### Screenshot 12 — Successful backend health or API response from the Web VM through the App VM’s private IP

Add your screenshot here.

---

# Task 6 — Deploy the Presentation or Web Tier

## Goal

Deploy the Next.js frontend on the existing Web VM on port **3000**. Configure Nginx on port **80** to forward page requests to the frontend and `/api/` requests to the Internal Load Balancer on port **3001**. Configure PM2 and verify recovery after reboot.

## Evidence

#### Screenshot 13 — Web VM overview or networking configuration showing its subnet and VM details

![ouput](./screenshots/wk7a6t6-ss13.png)

![ouput](./screenshots/wk7a6t6-ss13a.png)

![ouput](./screenshots/wk7a6t6-ss13b.png)

---

#### Screenshot 14 — Nginx site configuration showing frontend and API routing, together with successful `sudo nginx -t` output

![ouput](./screenshots/wk7a6t6-ss14.png)

![ouput](./screenshots/wk7a6t6-ss14a.png)
From the above, given my setup (Burstable tier, single-zone, no explicit HA config), this will likely show HA as disabled — which is expected and appropriate for a lab/learning deployment (worth noting honestly my your submission if the assignment expects production-grade HA). 
**Enabled, changes my set-up to a paid, higher-cost configuration.**

![ouput](./screenshots/wk7a6t6-ss14b.png)

![ouput](./screenshots/wk7a6t6-ss14c.png)



---

#### Screenshot 15 — PM2 showing `book-review-frontend` online after reboot, with Nginx active and a successful local HTTP response

![ouput](./screenshots/wk7a6t6-ss15.png)


---

# Task 7 — Configure Load Balancers, Health Checks, and Monitoring

## Goal

Configure a Standard Public Load Balancer using TCP **80 → 80** and a Standard Internal Load Balancer using TCP **3001 → 3001**. Verify backend health, update the allowed frontend origin, and configure monitoring and at least one alert rule.

## Evidence

#### Screenshot 16 — Public Load Balancer frontend IP, web backend membership, TCP 80 → 80 rule, and health-probe status

**listener/rule**
![ouput](./screenshots/wk7a6t7-ss16-lis.png)

**Frontend point** 
![ouput](./screenshots/wk7a6t7-ss16-front.png)

**Healthy Web targets**
![ouput](./screenshots/wk7a6t7-ss16-health.png)

---

#### Screenshot 17 — Internal application-tier load-balancing or routing configuration where applicable
1. Frontend IP  internal 2.listener 3.Backend pool
![ouput](./screenshots/wk7a6t7-ss17.png)

![ouput](./screenshots/wk7a6t7-ss17a.png)

![ouput](./screenshots/wk7a6t7-ss17b.png)

![ouput](./screenshots/wk7a6t7-ss17c.png)

![ouput](./screenshots/wk7a6t7-ss17d.png)

---

#### Screenshot 18 — Monitoring evidence showing a relevant metric or collected log and the configured alert rule

![ouput](./screenshots/wk7a6t7-ss18.png)

---

# Task 8 — Validate the Production-Style Deployment

## Goal

Verify registration, login, book viewing, and persistent review creation through the Public Load Balancer. Confirm private-tier restrictions and automatic service recovery after controlled App VM and Web VM restarts. Downtime is expected during these single-instance restart tests.

## Evidence

#### Screenshot 19 — Browser showing the Book Review App through the Public Load Balancer endpoint, with the address bar visible

![ouput](./screenshots/wk7a6t8-ss19.png)

---

#### Screenshot 20 — Successful registration/login, book viewing, and review creation, with database evidence that the review persists

![ouput](./screenshots/wk7a6t8-ss20.png)

---

#### Screenshot 21 — App VM networking showing no Public IP, MySQL private-access settings, and relevant NSG restrictions

**The bookreview VM is assigned only a
private 10.0.2.4 address and has no public IP, preventing direct Internet access to the Application Tier.**

![ouput](./screenshots/wk7a6t8-ss21.png)

---

#### Screenshot 22 — Post-restart PM2/service status, healthy Load Balancer backends, and a successful application response

![ouput](./screenshots/wk7a6t8-ss22.png)

![ouput](./screenshots/wk7a6t8-ss22a.png)

![ouput](./screenshots/wk7a6t8-ss22c.png)

Azure Load Balancer Insights confirms that the Web VM registered in WebBackendPool is responding successfully to the TCP port 80 health probe and is available to receive traffic.

---

#### Public Endpoint

Paste your Public Load Balancer website URL here:

**http://40.88.208.183/**

---

#### Recovery Notes

Record which VM was restarted, the observed interruption, recovery time, and whether services recovered without manual startup.

**Deployment Summary: What Worked, Issues Encountered, and Design Choices**

**What Worked**
The Azure Book Review application was successfully implemented as a three-tier architecture consisting of a Web Tier, Application Tier, and Database Tier. Network separation was achieved using dedicated Web, App, and Database subnets inside `Book-Review-VNet`.

The Web Tier successfully ran the Next.js frontend behind Nginx, while the Application Tier ran the Node.js/Express backend using PM2 for process persistence. The backend connected successfully to Azure Database for MySQL Flexible Server using SSL. PM2 was configured so that the frontend and backend could restart automatically after process failures or VM reboots.

End-to-end testing was performed progressively using `curl`: first against the backend on `localhost:3001`, then the frontend on `localhost:3000`, and finally through Nginx using `/api/books`. Browser testing was also used to verify book retrieval, registration, login, book details, reviews, and review submission.

Database connectivity was additionally verified directly from the App VM. A SQL query against `book_review_db` returned a `book_count` of `3`, providing direct evidence that the private App Tier could successfully read data from Azure MySQL.

**Issues Encountered and How They Were Fixed**
One Azure-specific problem was `PublicIPCountLimitReached`. The subscription had already reached its regional public-IP quota. Instead of increasing the quota, an unused public IP belonging to an unrelated resource was removed, allowing the required public IP to be created.

Another issue involved Nginx continuing to display its default page even though the custom configuration existed. The problem was that the `book-review` configuration had to be linked into `/etc/nginx/sites-enabled/`. The configuration was enabled using a symbolic link, the default Nginx site was removed, `nginx -t` was used to validate the configuration, and Nginx was restarted.

API routing also required careful configuration. Browser testing exposed an `/api/api/` path-doubling problem. The frontend was therefore configured with: `NEXT_PUBLIC_API_URL=/api`

and Nginx handled `/api/` traffic before forwarding it to the private Application Tier. The frontend environment variable also had to exist before `npm run build`, because Next.js embeds public environment variables during the production build.

**There was also a port inconsistency in the assignment.** Earlier sections used backend port `5000`, while the detailed deployment configured the application on port `3001`. The deployed configuration therefore needed the backend `.env`, App NSG, Internal Load Balancer rule/probe, and Nginx proxy configuration to use the same application port.

**vailability Choices**
Availability was supported through the Public and Internal Azure Load Balancers and their health probes. The Public Load Balancer checked the Web Tier on TCP port 80, while the Internal Load Balancer provided a private frontend for the Application Tier. Health probes were used to determine whether backend targets were available before receiving traffic.

PM2 added application-level resilience by keeping the Node.js processes running independently of SSH sessions and automatically restarting them after failures or reboots.

The assignment's high-level database plan includes zone-redundant High Availability and an optional read replica. However, the detailed deployment evidence does not clearly confirm that zone-redundant HA or a read replica was actually enabled, so these should not be claimed as completed unless the Azure Portal configuration proves them.

### Security Choices

Security followed a least-privilege three-tier model. The Web Tier accepted HTTP/HTTPS traffic and restricted SSH to the administrator's public `/32` address. :contentReference[oaicite:9]{index=9}

The App VM had no public IP and was accessible administratively through the Web VM as a jump host. The Database Tier used Azure MySQL Private Access with VNet Integration and accepted MySQL traffic only from the App subnet. :contentReference[oaicite:10]{index=10}

Database communication used SSL on port `3306`, protecting data in transit between the backend and Azure MySQL. :contentReference[oaicite:11]{index=11}

The App subnet used a NAT Gateway for controlled outbound Internet access without exposing the App VM directly to incoming Internet connections.

**Secrets Choices**
Database credentials and application secrets were kept outside the source code in the backend `.env` file. Variables included `DB_HOST`, `DB_USER`, `DB_PASS`, `JWT_SECRET`, and `ALLOWED_ORIGINS`. A random JWT secret was generated using:

`openssl rand -hex 32`

This prevented passwords and authentication secrets from being hardcoded into the application source. :contentReference[oaicite:12]{index=12}

The documented implementation uses `.env` rather than Azure Key Vault. Therefore, Azure Key Vault should only be described as a future improvement unless it was separately configured.

**Monitoring Choices**
Monitoring in the documented implementation focused on operational checks rather than a dedicated Azure Monitor deployment. Load Balancer health probes were used for backend health, while `pm2 status`, `systemctl status nginx`, `nginx -t`, application logs, and layered `curl` tests were used to verify service health and isolate failures.

**Backup Choices**
Azure Database for MySQL Flexible Server was chosen partly because it is a managed database service in which Azure handles underlying maintenance, patching, and backups.

The assignment does not document a custom backup-retention period, geo-redundant backup configuration, or a restore test. Therefore, the supported statement is that the deployment relies on the managed backup capability of Azure MySQL Flexible Server; specific retention or disaster-recovery settings should only be added if they are verified in the Azure Portal.

---

#### Deployment Notes

Summarize what worked, issues encountered, fixes applied, and sources used. Identify the backend route used for direct and Internal Load Balancer verification, including its expected response.

Write your answer here.

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post with an application screenshot, your public website URL, and a caption explaining what you deployed, what you learned, one challenge you resolved, and how you verified database-backed functionality and recovery after VM restarts.

Include **#DMIByPravinMishra #Azure #DevOps #CloudComputing**. Add the credit note and tags for your track as specified in the assignment guidelines.

## Evidence

#### LinkedIn Post URL

Paste your published LinkedIn post URL here:

`Add your URL here`

---

#### Screenshot 23 — Published LinkedIn post showing your caption, website URL, and application screenshot

Add your screenshot here.

---

# Submission Instructions

- Include your full name and Screenshots **1–23** under the corresponding evidence headings.
- Use multiple captures under an evidence heading where needed for readability.
- Include the working **public endpoint URL** and published **LinkedIn post URL**.
- Complete the architecture, database, recovery, and deployment notes.
- Ensure screenshots are readable and show your own environment.
- Do not expose passwords, private keys, tokens, connection strings containing credentials, subscription IDs, or other sensitive information.
- Submit your work using the method specified in the assignment guidelines.

---

# Completion Checklist

- [x] Task 1: Architecture diagram and assumptions documented (Screenshots 1–2)
- [x] Task 2: Network foundation created with isolated tiers (Screenshots 3–5)
- [x] Task 3: Least-privilege security and secret management configured (Screenshots 6–7)
- [x] Task 4: Presentation tier deployed (Screenshots 8–9)
- [x] Task 5: Application tier deployed privately (Screenshots 10–12)
- [x] Task 6: Managed database tier deployed privately (Screenshots 13–15)
- [x] Task 7: Public entry, internal routing, and monitoring configured (Screenshots 16–18)
- [x] Task 8: End-to-end validation and availability test completed (Screenshots 19–22, Public Endpoint, Notes)
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
