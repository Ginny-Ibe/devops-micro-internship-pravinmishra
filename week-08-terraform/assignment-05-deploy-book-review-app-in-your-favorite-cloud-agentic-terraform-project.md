# Capstone Assignment — Deploy the Book Review App Using Terraform and Claude Code Agentic AI

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Ginny Ibe
**Cloud Platform:** AWS or Azure  
**GitHub Repository URL:** https://github.com/Ginny-Ibe
**Public Application URL / Load-Balancer DNS:** Add the public URL or DNS here

---

## Purpose

Deploy the Book Review App using Terraform on AWS or Azure in a secure, highly available, production-style three-tier architecture. Use Claude Code, specialized subagents, Terraform MCP, and validation hooks to support the engineering workflow while keeping all infrastructure-changing operations under human control.

---

# Task 0 — Prepare the Project and Agentic AI Environment

## Goal

Prepare the Book Review App project and configure the provided Claude Code Agentic AI starter kit with project context, specialized subagents, Terraform MCP, validation hooks, and safety guardrails.

## Evidence

### Screenshot 1 — Project `CLAUDE.md`

Add a screenshot of the project `CLAUDE.md` showing the three-tier architecture, security boundaries, Terraform requirements, and human-approval rules.

![ouput](./screenshots/wk8a5t0-ss1.png)
![ouput](./screenshots/wk8a5t0-ss1a.png)
![ouput](./screenshots/wk8a5t0-ss1b.png)

---

### Screenshot 2 — Terraform Engineer Subagent

Add a screenshot showing the Terraform Engineer subagent configuration.

![ouput](./screenshots/wk8a5t0-ss2.png)

---

### Screenshot 3 — Architecture and Security Reviewer Subagent

Add a screenshot showing the Architecture and Security Reviewer subagent configuration.

![ouput](./screenshots/wk8a5t0-ss3.png)
![ouput](./screenshots/wk8a5t0-ss3a.png)
---

### Screenshot 4 — Terraform MCP Connection

Add a screenshot showing Terraform MCP connected and available.

![ouput](./screenshots/wk8a5t0-ss4.png)

---

### Screenshot 5 — Validation Hooks

Add a screenshot showing the configured Claude Code validation hooks.

![ouput](./screenshots/wk8a5t0-ss5.png)

---

# Task 1 — Design the Three-Tier Architecture

## Goal

Design the required secure, highly available three-tier architecture and create an architecture diagram before building the infrastructure.

The diagram must show:

- VPC or VNet
- Availability Zones or equivalent availability locations
- Six subnets
- Internet connectivity
- NAT or outbound design
- Public load balancer
- Web Tier
- Internal load balancer
- Application Tier
- Managed MySQL
- Read replica
- Main traffic flow

## Architecture Diagram

![ouput](./screenshots/wk8a5t1-diag.png)


---

# Task 2 — Build the Terraform Networking and Security Layers

## Goal

Create the modular Terraform project and implement the network and security layers across the required public and private subnets.

## Evidence

### Screenshot 6 — Modular Terraform Project Structure

Add a screenshot showing the modular Terraform project structure.

![ouput](./screenshots/wk8a5t2-ss6.png)

---

### Screenshot 7 — Six-Subnet Architecture

Add a screenshot showing the six-subnet architecture across two availability locations.

![ouput](./screenshots/wk8a5t2-ss7.png)
![ouput](./screenshots/wk8a5t2-ss7a.png)
![ouput](./screenshots/wk8a5t2-ss7b.png)
![ouput](./screenshots/wk8a5t2-ss7c.png)

---

### Screenshot 8 — Public and Private Tier Separation

Add a screenshot showing the public and private tier separation, including routing and security boundaries.

![ouput](./screenshots/wk8a5t2-ss8.png)
![ouput](./screenshots/wk8a5t2-ss8a.png)
![ouput](./screenshots/wk8a5t2-ss8b.png)

---

# Task 3 — Build the Load-Balancing and Compute Layers

## Goal

Deploy the public and internal load balancers and the Web and Application compute resources required by the Book Review App.

## Evidence

### Screenshot 9 — Web and Application Compute

Add a screenshot showing the Web and Application compute resources in their required subnets.

![ouput](./screenshots/wk8a5t3-ss9.png)

![ouput](./screenshots/wk8a5t3-ss9a.png)

---

### Screenshot 10 — Public Load Balancer

Add a screenshot showing the internet-facing public load balancer.

![ouput](./screenshots/wk8a5t3-ss10.png)

![ouput](./screenshots/wk8a5t3-ss10a.png)

---

### Screenshot 11 — Internal Load Balancer

Add a screenshot showing the private internal load balancer.

![ouput](./screenshots/wk8a5t3-ss11.png)

---

### Screenshot 12 — Healthy Targets

Add a screenshot showing healthy target groups or backend pools.

![ouput](./screenshots/wk8a5t3-ss12.png)

---

# Task 4 — Build the Managed MySQL Database Layer

## Goal

Deploy a private, highly available managed MySQL database with a read replica and restrict database connectivity to the Application Tier.

## Evidence

### Screenshot 13 — Managed MySQL Database

Add a screenshot showing the managed MySQL database deployment.

![ouput](./screenshots/wk8a5t4-ss13.png)

---

### Screenshot 14 — High Availability

Add a screenshot showing the Multi-AZ or high-availability configuration.

![ouput](./screenshots/wk8a5t4-ss14.png)
![ouput](./screenshots/wk8a5t4-ss14a.png)
---

### Screenshot 15 — Read Replica

Add a screenshot showing the read replica configuration.

![ouput](./screenshots/wk8a5t4-ss15.png)

![ouput](./screenshots/wk8a5t4-ss15a.png)

---

### Screenshot 16 — Private Database Access

Add a screenshot showing that the database is private and accepts MySQL traffic only from the Application Tier.

![ouput](./screenshots/wk8a5t4-ss16.png)

![ouput](./screenshots/wk8a5t4-ss16a.png)

---

# Task 5 — Validate, Review, and Apply the Terraform Configuration

## Goal

Validate the Terraform configuration, review the execution plan using both Agentic AI and human judgment, and apply the infrastructure changes only after all required checks pass.

## Evidence

### Screenshot 17 — Terraform Validation

Add a screenshot showing successful `terraform validate` output.

![ouput](./screenshots/wk8a5t5-ss17.png)

---

### Screenshot 18 — Terraform Plan

Add a screenshot showing the Terraform plan output.

![ouput](./screenshots/wk8a5t5-ss18.png)
![ouput](./screenshots/wk8a5t5-ss18a.png)
![ouput](./screenshots/wk8a5t5-ss18b.png)

---

### Screenshot 19 — Terraform Apply

Add a screenshot showing successful `terraform apply` completion.

![ouput](./screenshots/wk8a5t5-ss19.png)
![ouput](./screenshots/wk8a5t5-ss19a.png)

---

# Task 6 — Deploy and Configure the Book Review Application

## Goal

Deploy and configure the Book Review App across the Web, Application, and Database tiers and verify the complete application functionality.

## Evidence

### Screenshot 20 — Homepage

Add a screenshot showing the Book Review App homepage through the public endpoint.

![ouput](./screenshots/wk8a5t6-ss20.png)

---

### Screenshot 21 — Login or Authentication

Add a screenshot showing successful login or authentication.

![ouput](./screenshots/wk8a5t6-ss21.png)

---

### Screenshot 22 — Book Data

Add a screenshot showing the book listing or book details.

![ouput](./screenshots/wk8a5t6-ss22.png)

---

### Screenshot 23 — Review Functionality

Add a screenshot showing the review functionality working successfully.

![ouput](./screenshots/wk8a5t6-ss23.png)

---

### Screenshot 24 — Backend or API Evidence

Add a screenshot showing that the backend or API is working successfully.

![ouput](./screenshots/wk8a5t6-ss24.png)
![ouput](./screenshots/wk8a5t6-ss24a.png)

---

### Screenshot 25 — Database Reads and Writes

Add a screenshot showing successful database reads and writes.

![ouput](./screenshots/wk8a5t6-ss25.png)
![ouput](./screenshots/wk8a5t6-ss25a.png)

## Public Application URL

**Public Application URL / DNS:** Add the working public application URL or load-balancer DNS here

---

# Task 7 — Demonstrate the Agentic AI Workflow

## Goal

Demonstrate how Claude Code assisted with Terraform generation, architecture and security review, and evidence-based troubleshooting while infrastructure-changing decisions remained under human control.

You do not need to submit your complete Claude Code conversation history. Include only focused evidence.

## Evidence

### Screenshot 26 — AI-Assisted Terraform Generation

Add a screenshot showing one useful example of AI-assisted Terraform generation or improvement.

![ouput](./screenshots/wk8a5t7-ss26.png)
![ouput](./screenshots/wk8a5t7-ss26a.png)

---

### Screenshot 27 — Architecture or Security Review

Add a screenshot showing one structured architecture or security review result.

![ouput](./screenshots/wk8a5t7-ss27.png)
![ouput](./screenshots/wk8a5t7-ss27a.png)
![ouput](./screenshots/wk8a5t7-ss27b.png)
---

### Screenshot 28 — AI-Assisted Troubleshooting

Add a screenshot showing one AI-assisted troubleshooting interaction based on collected evidence.

![ouput](./screenshots/wk8a5t7-ss28.png)
![ouput](./screenshots/wk8a5t7-ss28a.png)
![ouput](./screenshots/wk8a5t7-ss28b.png)

---

# Task 8 — Complete the Final Architecture Review

## Goal

Review the completed infrastructure against the original capstone requirements and resolve significant architecture, security, reliability, and cost issues.

Confirm that the final review covers:

- Tier separation
- Availability
- Public exposure
- Routing
- Security rules
- Load balancing
- Database privacy
- Secrets
- Terraform quality
- Module structure
- Reliability
- Obvious cost risks

Use Screenshot 27 as the focused evidence for the structured architecture or security review.

---

# Task 9 — Answer the Reflection Questions

## Goal

Reflect on the architecture, Terraform implementation, and Agentic AI workflow. Answer each question briefly in your own words.

## Architecture

### 1. Why did you separate the Web, Application, and Database tiers?

To limit blast radius. Each tier only talks to the tier next to it — Internet → Web (via the App Gateway) → App (via the internal load balancer) → MySQL — so a compromise of the public-facing Web tier doesn't hand an attacker a direct path to the database. It also lets each tier have different exposure rules: only Web needs to be reachable from the internet at all.

### 2. Why is the Application Tier private?

The Express backend on port 3001 is the only thing allowed to talk to the database, so it shouldn't be reachable from outside at all. It has no public IP and NSG rules only allow inbound traffic from the Web tier and the load balancers — there's no way to reach it directly from the internet, even if someone knew the port.

### 3. Why is MySQL private?

It holds the actual application data, the highest-value target. public_network_access is disabled entirely, and NSG rules on the DB subnet only allow inbound 3306 from the App tier specifically, with explicit deny rules for both VNet-wide and internet traffic as backstops. Even leaked credentials wouldn't be usable from outside the App subnet.

### 4. Why are multiple Availability Zones used?

Azure AZs are physically separate datacenters (independent power, cooling, networking) within one region. Spreading the Web/App VMSS instances and the DB's HA standby across two AZs means one datacenter going down doesn't take the whole app with it.

### 5. What is the difference between Multi-AZ/high availability and a read replica?

HA keeps a synchronously-replicated standby of the same primary for automatic failover — if the primary fails, the standby takes over with little to no data loss, but it isn't queryable on its own and doesn't add read capacity. A read replica is a separate, independently-connectable server that copies data from the primary asynchronously — it's for scaling out read traffic, not failover, and it can lag slightly behind the primary since replication isn't instant.

## Terraform

### 6. How did you divide your Terraform into modules?

By responsibility, matching the required build order: network, security, load-balancer, database, compute, keyvault. Each module owns exactly one concern — network only creates the VNet/subnets, security only creates NSGs and rules — so a change to one layer doesn't touch unrelated files.

### 7. How do the modules communicate through variables and outputs?

Root main.tf wires them together: one module's output becomes another's input variable — e.g., module.network's subnet IDs feed into module.database, and module.compute's VMSS managed-identity principal ID feeds into module.keyvault so it can grant that identity access. No module reaches into another's resources directly; everything crosses the boundary explicitly, so the dependency graph stays visible.

### 8. What did you specifically check in `terraform plan`?

Beyond fmt/validate: any unexpected public IP or 0.0.0.0/0 rule, anything that would expose port 3001 or 3306 outside its intended tier, any destroy/forces replacement on a resource that's currently live, and, after this session's password-rotation incident — specifically grepping the plan for administrator_password/Key Vault secret diffs before approving anything, since that turned out to be the actual recurring failure mode.

## Agentic AI

### 9. What was the purpose of `CLAUDE.md`?

It's the checked-in, durable contract for how Claude Code should behave on this project: the required architecture, the build sequence, safety rules (never auto-apply, never auto-destroy, never expose secrets), and the validation workflow. It's why Claude stopped and asked before applying a plan that would've silently rotated the DB password, instead of just proceeding.

### 10. What work did the Terraform Engineer subagent perform?

Implementing and fixing actual Terraform code — writing module resource blocks (like the Bastion IP Connect fix and the adopted read-replica resource), running fmt/validate/plan, and checking current provider documentation before writing arguments instead of recalling them from memory.

### 11. What did the Architecture and Security Reviewer identify?

Two untracked, actively-billing MySQL read replicas that existed in Azure but nowhere in Terraform state; and a structural defect where the database password could get silently rotated as a side effect of unrelated applies, traced through several real incidents in the session's own plan/apply logs. It also flagged that`.tfplan` files weren't gitignored despite being able to contain the plaintext DB password internally.

### 12. Why did you use Terraform MCP instead of relying only on Claude's existing Terraform knowledge?

Because provider arguments change between versions, and a wrong guess doesn't fail loudly — it either breaks mid-apply or silently does the wrong thing. This actually happened: a web-search-sourced service_endpoints argument turned out to be from an older provider release; this project's pinned provider (~> 5.5) uses a different service_endpoint block shape entirely. Checking the real, currently-installed provider schema instead of trusting memory or a search result is what caught it before it reached an apply.

### 13. What was the purpose of your validation hooks?

To catch problems at the cheapest possible point — formatting and typos before anything is evaluated, schema errors before any API call, full resource diffs before anything touches real Azure infrastructure — so that by the time I actually review and approve an apply, it's already been mechanically checked, and I can spend my attention on judgment calls a tool can't make (is this diff actually intended).

### 14. Describe one real issue Claude helped you troubleshoot.

After restarting the backend with a refreshed database password, the app started returning 502 Bad Gateway instead of connecting. Rather than guessing, Claude traced it to a shell heredoc escaping bug in its own earlier instructions — an escaped \$DB_PASS had written the literal text "$DB_PASS" into the app's config instead of the real password, which meant the backend's DB connection failed at startup and it never even started listening on its port. It asked me to confirm via pm2 logs before proposing the fix, rather than just applying a guess.

### 15. Describe one recommendation you reviewed, modified, or rejected instead of accepting blindly.

When importing an orphaned MySQL replica into Terraform, the first version of the resource block produced a plan that would have destroyed and recreated the live, working replica — a known provider import limitation. I didn't apply it; the code got corrected (missing subnet/DNS/storage arguments added, an ignore_changes guard added for the field Azure never returns) until a second plan showed zero drift. Separately, Claude's first attempt at a Key Vault service-endpoint argument failed terraform validate outright — instead of accepting that as final, it checked the actual installed provider schema and replaced it with the correct block syntax.

---

# Task 10 — Publish the Mandatory LinkedIn Post

## Goal

Publish a LinkedIn post describing the capstone, the technical work completed, the Agentic AI workflow, and the lessons learned.

Write the post in your own words, include at least one project image or other proof, and ensure that it can be viewed by the submission reviewer.

## LinkedIn Post URL

**LinkedIn Post URL:** **https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-agenticai-terraform-activity-7505715005628174336-xPKm?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGTqulMBvpSBQMnxbzFBrJkA0C9nlWM_uqM**

## Screenshot


![ouput](./screenshots/wk8a5tlink-ss.png)


---

# Submission Instructions

- Complete Tasks 0–10 in sequence.
- Include all Screenshots 1–28 exactly as specified.
- Ensure that your full name is visible in the required screenshots.
- Include the selected cloud platform.
- Include the completed architecture diagram.
- Include the modular Terraform project structure.
- Include the working public application URL or public load-balancer DNS.
- Include all required Agentic AI workflow evidence.
- Answer all 15 reflection questions briefly in your own words.
- Include the published LinkedIn post URL.
- Do not expose cloud credentials, database passwords, SSH private keys, JWT secrets, access tokens, account IDs, Terraform state containing sensitive values, or other confidential information.
- Review all screenshots and project files carefully before submitting through GitHub.

---

# Completion Checklist

- [x] Selected AWS or Azure
- [x] Added and reviewed the Agentic AI starter files
- [x] Configured `CLAUDE.md`
- [x] Configured the Terraform Engineer subagent
- [x] Configured the Architecture and Security Reviewer subagent
- [ ] Connected Terraform MCP
- [ ] Configured validation hooks and safety guardrails
- [ ] Created the architecture diagram
- [ ] Created the six-subnet design
- [ ] Configured public Web Tier routing
- [ ] Kept the Application Tier private
- [ ] Kept the Database Tier private
- [ ] Configured tier-specific Security Groups or NSGs
- [ ] Restricted backend port `3001`
- [ ] Restricted MySQL port `3306` to the Application Tier
- [ ] Created the public load balancer
- [ ] Created the internal load balancer
- [ ] Configured listeners and health checks
- [ ] Deployed the Web Tier compute resources
- [ ] Deployed the private Application Tier compute resources
- [ ] Provisioned private managed MySQL
- [ ] Configured Multi-AZ or high availability
- [ ] Configured a read replica
- [ ] Created the modular Terraform project
- [ ] Used variables, outputs, and module dependencies
- [ ] Used current Terraform documentation through MCP
- [ ] Used hooks for deterministic validation
- [ ] Completed `terraform fmt`
- [ ] Completed `terraform validate`
- [ ] Reviewed `terraform plan`
- [ ] Completed the Terraform Engineer review
- [ ] Completed the Architecture and Security review
- [ ] Applied the infrastructure only after human approval
- [ ] Deployed and configured the backend
- [ ] Deployed and configured the frontend
- [ ] Configured Nginx where required
- [ ] Configured the internal backend endpoint
- [ ] Configured the public frontend endpoint
- [ ] Verified the homepage
- [ ] Verified login or authentication
- [ ] Verified book data
- [ ] Verified review functionality
- [ ] Verified the backend API
- [ ] Verified database reads and writes
- [ ] Verified healthy load-balancer targets
- [ ] Included AI-assisted Terraform generation evidence
- [ ] Included one architecture or security review
- [ ] Included one AI-assisted troubleshooting example
- [ ] Completed the final architecture review
- [ ] Answered all 15 reflection questions
- [ ] Published the mandatory LinkedIn post
- [ ] Added the LinkedIn post URL
- [ ] Captured all 28 required screenshots
- [ ] Confirmed that my full name is visible in the required screenshots
- [ ] Checked that no secrets or sensitive information are exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory), focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations through hands-on experience.

---

## Resources

- Book Review App Repository: [https://github.com/pravinmishraaws/book-review-app](https://github.com/pravinmishraaws/book-review-app)
- DMI Official Website: [https://dmi.pravinmishra.com](https://dmi.pravinmishra.com)
- University: [https://university.pravinmishra.com](https://university.pravinmishra.com)
- Discord Community: [https://discord.pravinmishra.com](https://discord.pravinmishra.com)
- Blog: [https://dmi.pravinmishra.com/blog](https://dmi.pravinmishra.com/blog)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra on LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory on LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
