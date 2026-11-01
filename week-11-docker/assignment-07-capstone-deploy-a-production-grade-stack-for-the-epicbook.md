# Assignment 7 — Capstone: Deploy a Production-Grade Stack for The EpicBook

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook application as a production-oriented Docker Compose stack on a cloud VM. You will use optimized container images, isolated networks, health checks, persistent MySQL storage, a selected reverse proxy, logging, backup and restore testing, and reliability procedures.

---

# Task 0 — App Discovery and Architecture

## Goal

Review the EpicBook repository and design the intended application architecture.

### Evidence

#### Screenshot 1 — EpicBook Project Structure

Add a terminal screenshot showing the EpicBook project structure after cloning the repository.

![ouput](./screenshots/wk11a7t0-ss1.png)
![ouput](./screenshots/wk11a7t0-ss1a.png)

---

#### Screenshot 2 — Architecture Diagram

Add a screenshot of your architecture diagram showing:

- Public user
- Reverse proxy
- Frontend
- Backend
- Database
- Docker networks
- Public and private ports
- Persistent database storage

Add your full name inside the diagram or as a clear caption below it.

![ouput](./screenshots/wk11a7t0-ss2.png)

---

#### Screenshot 3 — Environment Variables and Ports Document

Add a screenshot showing the contents of:

```text
docs/02-env-and-ports.md
```

It must document environment-variable names, internal ports, persistent-data details, and the health-check method. Do not expose real credentials or values.

![ouput](./screenshots/wk11a7t0-ss3.png)

---

# Task 1 — Create Production Docker Images

## Goal

Create optimized production images for the EpicBook backend and frontend.

### Evidence

#### Screenshot 4 — Backend Dockerfile

Add a screenshot showing `backend/Dockerfile`, including:

- Dependency stage
- Minimal runtime stage
- Production startup command
- Internal backend port
- Non-root user configuration

![ouput](./screenshots/wk11a7t1-ss4.png)

---

#### Screenshot 5 — Frontend Dockerfile

Add a screenshot showing `frontend/Dockerfile`, including:

- Nginx runtime image
- Static frontend files copied to the Nginx web root

![ouput](./screenshots/wk11a7t1-ss5.png)

---

#### Screenshot 6 — Docker Ignore Files

Add a screenshot showing both:

```text
backend/.dockerignore
frontend/.dockerignore
```

![ouput](./screenshots/wk11a7t1-ss6.png)

---

#### Screenshot 7 — Docker Image Builds and Size Comparison

Add a terminal screenshot showing successful builds of:

- Baseline backend image
- Optimized backend image
- Frontend image

The screenshot must also show the baseline and optimized backend image-size comparison.

![ouput](./screenshots/wk11a7t1-ss7.png)
![ouput](./screenshots/wk11a7t1-ss7a.png)
![ouput](./screenshots/wk11a7t1-ss7b.png)

---

#### Screenshot 8 — Backend Running as Non-Root User

Add a terminal screenshot showing the optimized backend container running as a non-root user.

![ouput](./screenshots/wk11a7t1-ss8.png)

---

### Notes

Write a short note covering:

- Baseline and optimized backend image sizes
- The image-size reduction achieved
- One Docker layer-caching optimization used
- The security benefit of running the backend as a non-root user

Image sizes: The baseline backend image (node:20, single-stage, npm install) is 1.68 GB (417 MB content). The optimized multi-stage image (node:20-alpine, npm ci --omit=dev) is 230 MB (53.3 MB content). The frontend image (nginx:alpine serving only public/) is 159 MB.

Reduction: about 86% smaller on disk (1.68 GB → 230 MB) and 87% smaller in content size. The savings come from the Alpine base image instead of full Debian, leaving out dev dependencies (nodemon, handlebars dev tooling), clearing the npm cache, and a .dockerignore that keeps node_modules, .git and .env out of the build context.

Layer-caching optimization: package.json and package-lock.json are copied and npm ci runs before COPY . .. Docker caches each layer, so when only application code changes, the dependency layer is reused and rebuilds take seconds instead of reinstalling every package.

Non-root security benefit: The optimized image ends with USER node, so the app runs as uid 1000 instead of root, and the app files are owned by node (--chown=node:node). If an attacker exploits a vulnerability in the app or a dependency, they get a low-privilege user inside the container. They can't install packages, modify system files, or easily escalate toward the host. The baseline image runs as root, so the same exploit would give full control of the container.


---

# Task 2 — Create the Docker Compose Stack and Networks

## Goal

Create one Docker Compose stack containing the reverse proxy, frontend, backend, and MySQL database.

### Evidence

#### Screenshot 9 — Docker Compose Services

Add a screenshot showing `docker-compose.yml` with all four services:

```text
reverse-proxy
frontend
backend
database
```

![ouput](./screenshots/wk11a7t2-ss9.png)

---

#### Screenshot 10 — Networks and Named Volume

Add a screenshot showing:

- `front-tier` network
- `back-tier` network
- `db_data` named volume

![ouput](./screenshots/wk11a7t2-ss10.png)

---

#### Screenshot 11 — Docker Compose Validation

Add a terminal screenshot showing successful Docker Compose validation without exposing environment-variable values or secrets.

![ouput](./screenshots/wk11a7t2-ss11.png)

---

# Task 3 — Configure Health Checks and Startup Dependencies

## Goal

Configure health checks and ensure services start only after their dependencies are healthy.

### Evidence

#### Screenshot 12 — Backend Health Endpoint

Add a screenshot showing the backend application configuration for the `/health` endpoint.

![ouput](./screenshots/wk11a7t3-ss12.png)

---

#### Screenshot 13 — MySQL and Backend Health Checks

Add a screenshot showing `docker-compose.yml` with health checks for MySQL and the backend.

![ouput](./screenshots/wk11a7t3-ss13.png)

---

#### Screenshot 14 — Frontend and Reverse-Proxy Health Checks

Add a screenshot showing:

- Frontend health check
- Reverse-proxy health check
- `depends_on` conditions using `service_healthy`

![ouput](./screenshots/wk11a7t3-ss14.png)

---

#### Screenshot 15 — Running Healthy Services

Add a terminal screenshot showing Docker Compose service status. The database, backend, frontend, and reverse proxy must be running successfully.

![ouput](./screenshots/wk11a7t3-ss15.png)

---

#### Screenshot 16 — Public Health Endpoint

Add a terminal screenshot showing a successful response from the public application health endpoint through the reverse proxy.

![ouput](./screenshots/wk11a7t3-ss16.png)

---

#### Screenshot 17 — Health-Check and Startup-Order Document

Add a screenshot showing the contents of:

```text
docs/03-healthchecks-and-depends-on.md
```

Explain the health-check method for each service and the startup dependency order.

![ouput](./screenshots/wk11a7t3-ss17.png)

---

# Task 4 — Configure the Reverse Proxy and Same-Origin Routing

## Goal

Use either Nginx or Traefik as the only public entry point for the EpicBook application.

### Evidence

#### Screenshot 18 — Selected Reverse-Proxy Configuration

Add a screenshot showing the configuration for your selected reverse proxy.

It must show routes for:

- Static frontend assets
- Application pages
- API requests
- Health endpoint

![ouput](./screenshots/wk11a7t4-ss18.png)

---

#### Screenshot 19 — Only Reverse Proxy Publishes Port 80

Add a screenshot of `docker-compose.yml` showing that only the `reverse-proxy` service publishes port 80.

![ouput](./screenshots/wk11a7t4-ss19.png)

---

#### Screenshot 20 — Reverse-Proxy Route Testing

Add a terminal screenshot showing successful requests through the selected reverse proxy to:

- Application page
- One API endpoint
- One static asset
- Health endpoint

![ouput](./screenshots/wk11a7t4-ss20.png)

---

#### Screenshot 21 — EpicBook Application Through Public IP

Add a browser screenshot showing the EpicBook application loaded through the VM public IP address.

Add your full name as a clear caption below the screenshot.

![ouput](./screenshots/wk11a7t4-ss21.png)
![ouput](./screenshots/wk11a7t4-ss21a.png)
![ouput](./screenshots/wk11a7t4-ss21b.png)

---

#### Screenshot 22 — Proxy Routing and CORS Document

Add a screenshot showing the contents of:

```text
docs/04-proxy-routing-and-cors.md
```

Explain the proxy routes and state whether CORS was required and why.

![ouput](./screenshots/wk11a7t4-ss22.png)
![ouput](./screenshots/wk11a7t4-ss22a.png)

---

# Task 5 — Prove Data Persistence, Backup, and Restore

## Goal

Verify MySQL persistence and perform a controlled backup and restore drill.

### Evidence

#### Screenshot 23 — MySQL Volume Configuration

Add a terminal screenshot showing the `db_data` named volume and its MySQL mount configuration.

![ouput](./screenshots/wk11a7t5-ss23.png)

---

#### Screenshot 24 — Test Data Before Backup

Add a terminal screenshot showing the selected test data before the backup and restore drill.

![ouput](./screenshots/wk11a7t5-ss24.png)

---

#### Screenshot 25 — Successful Backup Creation

Add a terminal screenshot showing successful backup creation and the backup file stored in the host backup directory.

![ouput](./screenshots/wk11a7t5-ss25.png)

---

#### Screenshot 26 — Controlled Data-Loss Test

Add a terminal screenshot showing that the selected test record was removed during the controlled data-loss test.

![ouput](./screenshots/wk11a7t5-ss26.png)

---

#### Screenshot 27 — Restore Verification

Add a terminal screenshot showing successful restore and verification that the deleted test record is available again.

![ouput](./screenshots/wk11a7t5-ss27.png)

---

#### Screenshot 28 — Persistence After Down/Up Cycle

Add a terminal screenshot showing that database data remains available after a non-destructive Docker Compose down/up cycle.

Do not use `docker compose down -v`.

![ouput](./screenshots/wk11a7t5-ss28.png)

---

#### Screenshot 29 — Persistence and Backup Document

Add a screenshot showing the contents of:

```text
docs/05-persistence-and-backup.md
```

Include the backup plan and restore procedure.

![ouput](./screenshots/wk11a7t5-ss29.png)
![ouput](./screenshots/wk11a7t5-ss29a.png)

---

# Task 6 — Configure Logging and Observability

## Goal

Configure useful reverse-proxy and backend logs without exposing sensitive information.

### Evidence

#### Screenshot 30 — Logging Configuration

Add a screenshot showing:

- Configuration for the selected reverse proxy
- Proxy log format
- Docker Compose host log-directory bind mount

![ouput](./screenshots/wk11a7t6-ss30.png)
![ouput](./screenshots/wk11a7t6-ss30a.png)

---

#### Screenshot 31 — Persistent Proxy Logs and Backend Logs

Add a terminal screenshot showing:

- Selected reverse-proxy logs available from the host directory after a proxy restart
- Backend logs displayed through Docker Compose

![ouput](./screenshots/wk11a7t6-ss31.png)
![ouput](./screenshots/wk11a7t6-ss31a.png)

---

### Notes

Write a short note covering:

- The selected reverse proxy
- Host path used for reverse-proxy logs
- How backend logs are viewed
- Whether JSON or standard text logs were used
- Why passwords, tokens, headers, and database connection strings must not appear in logs

Selected reverse proxy: Nginx (nginx:alpine), the only service publishing a port (80).

Host path for reverse-proxy logs: ./logs/nginx/ in the project folder (access.log and error.log), bind-mounted to /var/log/nginx in the container. The logs are written straight to the host disk, so they can be read with cat/tail without docker exec and survive container restarts and recreation. This was tested: 6 lines before docker compose restart reverse-proxy, still 6 after.

How backend logs are viewed: the Node/Express backend writes to stdout, and Docker captures it. Logs are viewed with docker compose logs backend (add -f to follow, --tail N to limit). All four services use Docker's json-file driver with rotation (max-size: 10m, max-file: 3), so stdout logs can't fill the disk.

Log format: the proxy uses structured JSON, one object per request (custom log_format json_safe with escape=json). Fields: time, client IP, method, path, status, bytes, request time, and which upstream container served the request with its status and response time. Each field is named, so tools like Fluent Bit or CloudWatch can parse the lines without guessing. The backend uses standard text (Sequelize and Express console output).

Why secrets must not appear in logs: logs are copied, shipped to other systems, kept for a long time and read by many people (developers, support, monitoring tools), usually with far less protection than the secrets themselves. A password, token, Authorization header, cookie or database connection string in a log line becomes a working credential for anyone who can read that log, and it's hard to purge once it's been shipped or backed up. So the proxy logs only the URL path ($uri, not $request, which would include the query string), and logs no headers, cookies or referer. This was tested: a request to /?token=SECRET123 was logged as "path":"/", and grep SECRET123 found 0 matches. Health-check requests use access_log off to keep noise out. The database connection string (JAWSDB_URL) is passed as an environment variable and never printed by the app.

---

# Task 7 — Deploy and Verify the Stack on a Cloud VM

## Goal

Deploy the completed Docker Compose stack on an AWS or Azure VM and verify public access.

### Evidence

#### Screenshot 32 — VM Public IP and Inbound Rules

Add a cloud-console screenshot showing:

- VM public IP address
- SSH port 22 restricted to your IP address
- HTTP port 80 allowed from Anywhere

![ouput](./screenshots/wk11a7t7-ss32.png)
![ouput](./screenshots/wk11a7t7-ss32a.png)
![ouput](./screenshots/wk11a7t7-ss32b.png)
![ouput](./screenshots/wk11a7t7-ss32c.png)

---

#### Screenshot 33 — Cloud VM Stack Verification

Add a VM terminal screenshot showing:

- Docker Compose service status
- Successful public health or API response
- No published database, frontend, or backend ports

![ouput](./screenshots/wk11a7t7-ss33.png)

---

#### Screenshot 34 — EpicBook Application on Cloud VM

Add a browser screenshot showing the EpicBook application loaded through the VM public IP address.

Add your full name as a clear caption below the screenshot.

![ouput](./screenshots/wk11a7t7-ss34.png)
![ouput](./screenshots/wk11a7t7-ss34a.png)

---

### Notes

Write a short note covering:

- Cloud provider used
- VM operating system
- Public port exposed
- Security rules configured
- Confirmation that the application and backend API worked through the reverse proxy

Cloud provider: AWS (us-east-1). Terraform provisioned a dedicated VPC, a public subnet, an internet gateway, a security group and a t3.micro EC2 instance (epicbook-capstone, 3.92.87.219).

VM OS: Ubuntu 22.04 LTS. Cloud-init installed Docker and the Compose plugin and added 2 GB of swap.

Public port: only 80, on the Nginx reverse proxy. The frontend, backend (8080) and MySQL (3306) are internal to Docker only. docker ps shows no host ports for them, and ss shows nothing listening on 8080 or 3306.

Security rules: inbound SSH 22 from my IP only (153.68.123.37/32) and HTTP 80 from anywhere. IMDSv2 is required and the root disk is encrypted.

Verification: all 4 services were healthy. curl http://3.92.87.219/health returned OK (200) through the proxy to the backend, and / returned 200. EpicBook loaded in the browser at http://3.92.87.219 with the books from MySQL and "Deployed by Ginny Ibe" on the page.

---

# Task 8 — Automate Deployment with CI/CD (Optional)

## Goal

Optionally automate image build, image push, and deployment through GitHub Actions or Azure Pipelines.

### Optional Evidence

#### Optional Screenshot — Successful CI/CD Pipeline Run

Add a screenshot showing a successful pipeline run with build, image push, deployment, and verification stages.

![ouput](./screenshots/wk11a7t8-ss37.png)

---

### Optional Notes

Write a short note covering:

- CI/CD platform used
- Image-tagging method
- Registry used
- Deployment trigger
- Manual approval or secret-handling approach

Platform: GitHub Actions, in 3 stages: build & push the backend and frontend images, deploy to EC2, verify the public endpoints.

 Tagging: every image gets the full git commit SHA plus latest. The VM always runs the SHA tag, so each container traces back to an exact commit and a rollback is just deploying an older SHA.

Registry: GitHub Container Registry (ghcr.io/ginny-ibe/epicbook-*). Pushes and pulls use the short-lived GITHUB_TOKEN.

Trigger: a push to capstone/epicbook-docker that changes app or compose files, or a manual run. The deploy runs docker compose pull and then up -d --wait. The database is never restarted.

Approval and secrets: a production environment with me as required reviewer gates the deploy. AWS access uses OIDC with no stored keys, and the role can only edit the security group. Port 22 opens to the runner's IP only during the deploy. The only secret is a dedicated SSH deploy key, and the database passwords stay in .env on the VM.

Result: all stages passed, and the VM runs epicbook-*:8a94ee5… with all 4 services healthy.


---

# Task 9 — Perform Reliability Tests and Create an Operations Runbook

## Goal

Test controlled service failures and document safe operating procedures.

### Evidence

#### Screenshot 35 — Backend Failure and Recovery

Add a terminal screenshot showing:

- Backend failure test
- Expected unavailable response through the reverse proxy
- Backend restart
- Successful health-check recovery

![ouput](./screenshots/wk11a7t9-ss35.png)

---

#### Screenshot 36 — Database Failure and Recovery

Add a terminal screenshot showing:

- Database outage test
- Failed database-dependent request
- Database restart
- Successful application recovery

![ouput](./screenshots/wk11a7t9-ss36.png)
![ouput](./screenshots/wk11a7t9-ss36a.png)
---

### Notes

Write a short operations runbook covering:

- Safe restart procedure for reverse proxy, frontend, backend, and database
- Backup and restore procedure
- Secret-rotation approach
- Database recovery procedure
- What to check when the application returns an error
- Results of backend and database reliability tests

1. **Safe restart procedure (run from ~/epicbook-docker-capstone on the VM)**

- Reverse proxy: docker compose restart reverse-proxy. After a config change, check it first with docker compose exec reverse-proxy nginx -t, then reload with no downtime: docker compose exec reverse-proxy nginx -s reload.
- Frontend: docker compose restart frontend. This only affects /assets/ (CSS, JS, images).
- Backend: docker compose restart backend, or after a code change, git pull && docker compose up -d --build --wait backend. The proxy re-resolves the backend's IP (resolver 127.0.0.11 valid=10s), so it doesn't need restarting.
- Database: take a backup first, then docker compose restart database. Data lives on the db_data named volume. Never run docker compose down -v, because it deletes that volume.
- Afterwards, always verify: docker compose ps (all healthy) and curl http://3.92.87.219/health → OK.

2. **Backup and restore**

- Backup: docker compose exec -T database sh -c 'mysqldump -uroot -p"$MYSQL_ROOT_PASSWORD" --single-transaction --routines bookstore' > backups/bookstore-$(date +%F-%H%M).sql. --single-transaction gives a consistent snapshot without locking tables. backups/ is git-ignored.
- Restore: pick one specific file (never a wildcard): docker compose exec -T database sh -c 'mysql -uroot -p"$MYSQL_ROOT_PASSWORD" bookstore' < backups/<chosen-file>.sql, then check the row count. I tested this in Task 6: deleting a row took the count from 54 to 53, and the restore brought it back to 54.

3. **Secret rotation**
 - Secrets live only in .env on the host (mode 600, git-ignored), generated with openssl rand -hex 16. The healthcheck uses $$MYSQL_ROOT_PASSWORD so the password doesn't show in docker inspect.
 - MYSQL_PASSWORD only applies when the volume is first created, so editing .env alone does not change the password. To rotate: (1) generate a new value, (2) ALTER USER 'epicbook'@'%' IDENTIFIED BY '<new>'; inside MySQL, (3) update DB_PASSWORD in .env, (4) docker compose up -d --wait backend to recreate the backend with the new connection URL, (5) check /. Rotate the root password the same way.

 4. **Database recovery**
  - Container down: docker compose start database, then docker compose up -d --wait. The backend recovers on its own (see the test results).
  - Data corrupted or lost: stop the backend, restore the latest good backup (step 2), start the backend, then verify the Book count and the home page.
 - Volume lost: docker compose up -d --wait re-creates the schema and seed data from db/*.sql. Then restore the latest backup on top to get the cart and other changes back

5. **What to check when the app returns an error**
  i. docker compose ps: which service isn't healthy or keeps restarting?
  ii. 502 means the proxy can't reach the backend. Check tail logs/nginx/error.log (for example backend could not be resolved) and docker compose logs --tail 50 backend.
  iii. 404 or missing CSS/images means checking the frontend container and the /assets/ route.
 iv. A backend log showing SequelizeConnectionError or ENOTFOUND database means checking the database: docker compose logs database and its healthcheck.
 v. Resources: free -h and df -h (t3.micro has 1 GB RAM plus 2 GB swap). Then restart only the affected service.

6. **Reliability test results**
 - Backend failure: after docker compose stop backend, the proxy stayed up and returned 502 for / and /health. Its error log showed backend could not be resolved. After docker compose start backend, health was back to 200 within about 2 seconds. Finding: the stop took 10 seconds and exited with 137 (SIGKILL), because Node runs as PID 1 and ignores SIGTERM. Fix: add init: true to the backend service.
 - Database failure: after docker compose stop database, GET / returned 502. The backend crashed on an unhandled promise rejection (the async route had no try/catch, and sequelize.sync() has no retry at startup), and Docker restarted it automatically. After docker compose start database, all services were healthy and GET / returned 200 on the first try, with all 54 books intact on the db_data volume. Improvements: add try/catch to return a clean 503, add startup retry logic, and add a DB check to /health (or a separate /ready endpoint).


---

# Final Public Application URL

**EpicBook URL:** <http://3.92.87.219/>


---

# GitHub Repository URL

**Your Fork or Repository URL:** <https://github.com/Ginny-Ibe/epicbook-docker-capstone.git>

---

# LinkedIn Requirement

## Goal

Create a professional LinkedIn post of 6–10 lines about your EpicBook capstone deployment.

Your post must include:

- The architectural decision that most improved reliability
- Your biggest image-size reduction, with numbers
- Key production-hardening lessons
- A deployment verification image

### Evidence

**LinkedIn Post URL:** <https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-devops-docker-ugcPost-7522493729484496896-cYlf/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGTqulMBvpSBQMnxbzFBrJkA0C9nlWM_uqM>
#### LinkedIn Post Screenshot

Add a screenshot of the published LinkedIn post showing the text body and deployment verification image.

![ouput](./screenshots/wk11a7tlink-ss0.png)
![ouput](./screenshots/wk11a7tlink-ss0a.png)

---

# Submission Checklist

- [x] EpicBook repository reviewed and architecture diagram created
- [x] Environment variables, ports, persistence, and health-check details documented
- [x] Backend and frontend production Dockerfiles created
- [x] Backend runs as a non-root user
- [x] Docker image-size comparison completed
- [x] Docker Compose stack includes reverse proxy, frontend, backend, and database
- [x] `front-tier` and `back-tier` networks configured
- [x] `db_data` named volume configured
- [x] MySQL, backend, frontend, and reverse-proxy health checks configured
- [x] Startup dependencies use `service_healthy`
- [x] Nginx or Traefik selected as the only public reverse proxy
- [x] Only reverse-proxy port 80 is publicly published
- [x] Same-origin routing configured and CORS used only when required
- [x] Backup, restore, and persistence testing completed
- [x] Reverse-proxy and backend logs verified
- [x] Cloud VM deployment verified through the public IP
- [x] Backend and database reliability tests completed
- [x] Screenshots 1–36 included
- [x] Required notes completed
- [x] LinkedIn post URL and screenshot included
- [x] Full name visible in required screenshots or captions
- [x] No passwords, tokens, private keys, account IDs, or other sensitive information exposed

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
