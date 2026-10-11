# Assignment — Deploy EpicBook with Terraform and Ansible Roles

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook web application using Terraform and Ansible roles.

Terraform provisions the cloud infrastructure, including one Ubuntu VM and one managed MySQL database. Ansible roles configure the VM, install required software, deploy the EpicBook application, configure Nginx, connect the app to the managed MySQL database, and verify the deployment.

---

# Task 1 — Set Up the Project Folder Layout

## Goal

Create the project folder structure for Terraform and Ansible roles.

Terraform will be used to provision the cloud infrastructure. Ansible roles will be used to configure the VM and deploy the EpicBook application.

### Evidence

#### Screenshot 1 — Terminal showing the completed `epicbook-prod` project structure

![ouput](./screenshots/wk9a5t1-ss1.png)
![ouput](./screenshots/wk9a5t1-ss1a.png)

---

### Notes

Answer the following in your own words:

**1. Which cloud provider did you choose for this assignment?**

- I chose Amazon Web Services (AWS), in the us-east-1 region. My AWS CLI was already authenticated, and I could reuse the Terraform patterns and SSH key setup from my earlier AWS deployments. AWS also has the managed services this project needs: EC2 for the Ubuntu server that runs EpicBook, and RDS for the managed MySQL database. Using a managed database means AWS handles the backups and patching for me.

---

**2. Why is it useful to keep Terraform files and Ansible files in separate folders?**

- Terraform and Ansible do different jobs. Terraform creates the infrastructure: the VPC, security groups, EC2 instance and RDS database. Ansible configures what runs on it: packages, Nginx and the EpicBook application. Keeping them in separate folders makes that split clear, so I always know where to look. If the server can't be reached, I check terraform/; if the app won't start, I check ansible/.

---

**3. What is the purpose of the `roles` directory in Ansible?**

  The roles/ directory splits configuration into reusable building blocks, each with one job. In this project:
- common prepares the server with baseline tools: git, curl, unzip and the MySQL client.
- nginx installs and configures Nginx as a reverse proxy.
- epicbook deploys the application, connects it to the database and runs it with PM2.

Each role has a standard structure (tasks/, handlers/, templates/), which Ansible finds automatically. That keeps site.yml short, because it just lists the roles in the order they should run. Because the roles get their values from variables instead of hardcoded settings, I could reuse the same nginx role for a different application just by changing the variables.


---

# Task 2 — Provision the Infrastructure with Terraform

## Goal

Run Terraform to provision the cloud infrastructure for the EpicBook deployment.

Terraform will create the VM, managed MySQL database, networking, security rules, and required outputs.

### Evidence

#### Screenshot 2 — `terraform apply` completed successfully

![ouput](./screenshots/wk9a5t2-ss2.png)

---

#### Screenshot 3 — Output of `terraform output`

![ouput](./screenshots/wk9a5t2-ss3.png)

---

#### Screenshot 4 — Azure Portal or AWS Console showing the VM running

![ouput](./screenshots/wk9a5t2-ss4.png)

---

#### Screenshot 5 — Azure Portal or AWS Console showing the managed MySQL database created

![ouput](./screenshots/wk9a5t2-ss5.png)
![ouput](./screenshots/wk9a5t2-ss5a.png)

---

### Notes

Answer the following in your own words:

**1. What resources did Terraform create for this assignment?**

Terraform created everything EpicBook needs in AWS (us-east-1):
- Networking:
  - a VPC (10.0.0.0/16)
  - one public subnet for the web server
  - two private subnets in different Availability Zones for the database
  - an Internet Gateway
  - a public route table and its association with the public subnet
- Security:
  - a web security group that allows HTTP (80) from anywhere and SSH (22) only from my controller's IP (/32)
  - a database security group that allows MySQL (3306) only from the web security group
- Compute:
  - an SSH key pair built from my public key
  - one Ubuntu 22.04 EC2 instance (t3.micro) in the public subnet with a public IP
- Database:
  - a DB subnet group made of the two private subnets
  - one managed MySQL 8.0 RDS instance (db.t3.micro) with the bookstore database and the bookadmin admin user. It is not publicly accessible.

---

**2. Why should you review `terraform plan` before running `terraform apply`?**

`terraform plan`is a dry run. It shows exactly what Terraform will create, change or destroy, without touching anything. Reviewing it gives you a chance to catch mistakes before they become real, billable resources:
- a wrong region
- SSH left open to 0.0.0.0/0
- an oversized instance
- a resource marked for destroy or replace that you didn't expect, which could delete a database and its data

The summary line (X to add, Y to change, Z to destroy) is a quick sanity check. For a brand-new project like this, it should show only additions and no destroys.

---

**3. Why should database passwords not be shown in Terraform output?**

Anything in terraform output gets printed to the terminal. From there it can end up in screenshots, CI/CD logs, shell history or shared documents, where anyone who sees it can use it. Anyone with the password can read or delete the application's data.

In this project:
- the password comes from terraform.tfvars, which Git ignores
- the password variable is marked sensitive = true
- the outputs expose only non-secret values (EC2 IP, DB host, DB name, username)
- Ansible gets the password from an encrypted Ansible Vault file instead of from Terraform output

One caveat: Terraform still stores the password in plain text in terraform.tfstate. That's why the state file is also excluded from Git and should be protected.

---

# Task 3 — Verify SSH Key-Based Access

## Goal

Verify that the cloud VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 6 — Successful SSH hostname check from the Ansible controller

![ouput](./screenshots/wk9a5t3-ss6.png)

---

### Notes

Answer the following in your own words:

**1. What command did you use to verify SSH access?**

.ssh -i ~/.ssh/terraform-aws-epicbook-vm-key ubuntu@184.193.19.212 hostname

- -i tells SSH which private key to use. My key is called terraform-aws-epicbook-vm-key, not the default id_ed25519, so SSH wouldn't find it on its own.
- ubuntu is the default login user for the Ubuntu 22.04 AMI the compute module uses.
- 184.193.19.212 is the EC2 public IP from terraform output ec2_public_ip.
- hostname runs one command on the VM and prints the result, so I don't need to open an interactive session.

---

**2. What proves that SSH key-based access worked successfully?**

The command printed the VM's hostname (ip-10-0-1-x, its private IP in the public subnet) and never asked for a password. That proves the whole chain works:
- The private key on my controller matches the public key Terraform registered with AWS (aws_key_pair).
- The web security group allows SSH from my IP (153.68.123.37/32).
- The VM booted and its SSH service is running.

Ansible connects over SSH the same way, so this confirms Ansible will be able to reach the host.

---

**3. What would you check if SSH returned `Permission denied (publickey)`?**

That error means the server was reached but rejected the key, so I'd check the authentication side:
1. Username: it must be ubuntu for Ubuntu AMIs. ec2-user is for Amazon Linux, and root isn't allowed.
2. Key pair: the private key passed with -i must match the public_key_path in terraform.tfvars. I'd compare ssh-keygen -y -f ~/.ssh/terraform-aws-epicbook-vm-key with the contents of the .pub file.
3. Key permissions: the private key must be chmod 600. SSH ignores keys that others can read, which happens with keys stored on /mnt/c. That's why I copied mine into WSL ~/.ssh.
4. Key path: confirm the file actually exists at that path.
5. More detail: run ssh -v ... to see which keys were offered and why each was rejected.

A timeout or connection refused is a different problem: network access rather than the key. For those I'd check that the security group allows my current public IP, using curl -4 ifconfig.me, and that the instance is running with a public IP.

---

# Task 4 — Create the Ansible Inventory and Configuration

## Goal

Create the Ansible inventory file and local Ansible configuration for the EpicBook VM.

The inventory tells Ansible which VM to manage and which SSH user to use.

### Evidence

#### Screenshot 7 — `inventory.ini` showing the VM under the `web` group

![ouput](./screenshots/wk9a5t4-ss7.png)

---

#### Screenshot 8 — Output of `ansible-inventory -i inventory.ini --graph`

![ouput](./screenshots/wk9a5t4-ss8.png)

---

#### Screenshot 9 — Output of `ansible web -i inventory.ini -m ping`

![ouput](./screenshots/wk9a5t4-ss9.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `inventory.ini`?**

`inventory.ini` tells Ansible which servers to manage and how to connect to them. In my project it defines a [web] group with one host, epicbook. The [web:vars] section sets connection settings (SSH user and key) for every host in that group.

The group name matters in two places:
- site.yml targets hosts: web.
- Ansible loads group_vars/web/ automatically because the name matches the group.

If I added a second web server later, I'd only need to add one line under [web].

---

**2. What does `ansible_host` store?**

It stores the real address Ansible connects to: the EC2 public IP from terraform output ec2_public_ip. epicbook is just a friendly name Ansible uses in its output and to refer to the host. Because the name and address are separate, I only need to update ansible_host if the IP changes, for example when the infrastructure is recreated. The host name stays the same everywhere else.

---

**3. What does `ansible_ssh_private_key_file` tell Ansible?**

It tells Ansible which private key to use for SSH, in my case .`~/.ssh/terraform-aws-epicbook-vm-key` It does the same job as the -i flag in my manual SSH test from Task 3. Together with ansible_user=ubuntu, it reproduces the exact connection I already proved works by hand. Without it, Ansible would only try default keys like id_ed25519 and fail with Permission denied (publickey).

---

**4. Why is `host_key_checking = False` used only for this temporary lab?**

The first time you connect to a new server, SSH asks you to confirm its host key fingerprint. Ansible can't answer that interactive prompt, so a playbook run against a new VM would hang or fail. Turning the check off avoids that. It's acceptable here because the lab VM is temporary: it gets a new IP and new host key each time Terraform recreates it.

In production it's unsafe. The host key check is what protects you from a man-in-the-middle attack, where something pretends to be your server and captures your credentials or commands. Real environments should keep the check on and pre-load the known fingerprints into known_hosts. A safer middle ground is StrictHostKeyChecking=accept-new, which trusts a new host the first time but rejects a key that later changes.

---

# Task 5 — Create the Main Ansible Playbook

## Goal

Create the main Ansible playbook that runs the required roles in the correct order.

The `site.yml` file will call the `common`, `nginx`, and `epicbook` roles.

### Evidence

#### Screenshot 10 — `site.yml` showing the roles in the correct order

![ouput](./screenshots/wk9a5t5-ss10.png)

---

#### Screenshot 11 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

![ouput](./screenshots/wk9a5t5-ss11.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `site.yml`?**

site.yml is the main playbook, the single entry point for the whole deployment. It does no configuration work itself. It says:
- which hosts to target (hosts: web, the group from inventory.ini)
- to run with elevated privileges (become: true)
- which roles to apply, and in what order

The real tasks live inside each role (roles/<name>/tasks/main.yml), so site.yml stays short and easy to read, and each role can be reused in other projects. Running ansible-playbook site.yml --ask-vault-pass deploys the entire server in one command.

---

**2. Why should the roles run in the order `common`, `nginx`, and `epicbook`?**

Ansible runs roles in the order they are listed, and each role depends on what the earlier ones set up:
- `common`goes first because it prepares the base system. It updates the apt cache and installs git, mysql-client and Node.js/npm. The epicbook role can't work without these. It needs git to clone the repo, npm to install dependencies and PM2, and the mysql client to import the SQL files.
- `nginx` goes second so the reverse proxy is installed and configured before the application comes up. Nginx listens on port 80 and forwards to 127.0.0.1:8080.
- `epicbook` goes last because deploying and starting the app only makes sense once the tools exist and the proxy is ready to send traffic to it.

If `epicbook` ran first, it would fail immediately because git, npm and the mysql client wouldn't be installed yet. `nginx` strictly depends only on the apt cache refresh from common. Its main reason for coming second is so that, by the time the app starts, the path to it is already in place.

---

**3. What does `become: true` allow Ansible to do?**

`become: true` lets Ansible use privilege escalation (sudo) on the VM. I connect as the normal ubuntu user, but many tasks need root access:
- installing packages with apt
- adding the NodeSource repository
- writing to /etc/nginx/
- managing services
- installing PM2 globally
- creating the PM2 systemd service

Setting it once at play level applies it to every task in all three roles. For the application itself I used become_user: ubuntu on the git, npm and PM2 tasks. That way EpicBook runs as the normal ubuntu user instead of root, which is safer: if the app were compromised, the attacker wouldn't automatically have root on the server.

---

# Task 6 — Create the `common` Role

## Goal

Create the `common` role to prepare the Ubuntu VM with the basic packages required for the EpicBook deployment.

This role handles the common server setup before Nginx and the application are configured.

### Evidence

#### Screenshot 12 — `roles/common/tasks/main.yml` showing the common setup tasks

![ouput](./screenshots/wk9a5t6-ss12.png)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `common` role?**

The `common` role prepares the base operating system. It installs what the other roles need, without doing anything specific to `nginx`or `epicbook`. In my project it:
- updates the apt package cache
- installs the baseline tools: git, curl, unzip, software-properties-common, mysql-client, ca-certificates, gnupg
- adds the NodeSource repository and installs Node.js 20 (with npm)

The rest of the deployment depends on these:
- git clones the EpicBook repository
- npm installs the app's dependencies and PM2
- mysql-client imports the database

The role is generic, so it could prepare almost any Ubuntu server for a Node.js app.

---

**2. Why should Nginx installation not be placed inside the `common` role?**

Each role should have one clear responsibility. Nginx isn't a base requirement; it's the web server and reverse proxy for this specific application. Putting it in common would cause three problems:
- Reuse: common could no longer be used on servers that don't need a web server, such as a database or worker host.
- Organisation: the Nginx install would be split from its configuration (the epicbook.conf.j2 template, enabling the site, removing the default site, nginx -t, and the reload handler), which belongs together.
- Troubleshooting: if Nginx breaks, I want to look in one place, the nginx role, and know that everything about Nginx is there.

Keeping it separate means each role can be changed, tested or reused on its own.

---

**3. Why is `mysql-client` useful in this deployment?**

The database is a managed RDS instance in a private subnet. There's no MySQL server on the VM, and RDS can't be reached from the internet. The EC2 instance is the only machine allowed through the RDS security group on port 3306. mysql-client gives the VM the mysql command, so Ansible can talk to RDS from there.

The epicbook role uses it to:
- check whether the database is already initialised by running SHOW TABLES; against bookstore
- import the schema and seed data (BuyTheBook_Schema.sql, author_seed.sql, books_seed.sql), but only on the first run, when no tables exist yet

It's also useful for troubleshooting. From the VM I can connect to RDS manually to confirm the hostname, username and password work, and to look at the tables and data directly. That helps tell a database problem apart from an application problem.

---

# Task 7 — Create the `nginx` Role

## Goal

Create the `nginx` role to install Nginx and configure it as a reverse proxy for the EpicBook application.

Nginx will receive browser traffic on port `80` and forward it to the EpicBook Node.js application running on the VM.

### Evidence

#### Screenshot 13 — `roles/nginx/tasks/main.yml` showing Nginx installation and site configuration tasks

![ouput](./screenshots/wk9a5t7-ss13.png)

---

#### Screenshot 14 — `roles/nginx/templates/epicbook.conf.j2` showing the reverse proxy configuration

![ouput](./screenshots/wk9a5t7-ss14.png)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `nginx` role?**

The`nginx`role installs Nginx and sets it up as the public entry point for EpicBook. It doesn't start or manage the application; that's the epicbook role's job. It:
- installs `nginx` with apt
- deploys epicbook.conf.j2 to /etc/nginx/sites-available/epicbook as a template
- enables the site with a symlink in sites-enabled
- removes Ubuntu's default site so it doesn't take over port 80
- validates the configuration with nginx -t
- makes sure Nginx is running and starts on boot

When the configuration changes, a handler reloads`nginx`. Because the handler only runs on a change, re-running the playbook doesn't restart `nginx` for no reason.

---

**2. Why is Nginx configured as a reverse proxy in this deployment?**

The Node.js app listens only on 127.0.0.1:8080, inside the server. Nginx listens on port 80, the standard HTTP port browsers use, and forwards each request to the app with proxy_pass. This gives several benefits:
- Security: the app is never exposed directly. The security group opens only ports 80 and 22, not 8080.
- Standard port: users visit http://184.193.19.212 without typing a port number.
- Client details: the proxy_set_header lines pass the real client IP, hostname and protocol through to the app.
- Room to grow: Nginx is the place to add HTTPS/SSL, caching, compression or load balancing across several app servers later, without changing the application.

---

**3. Why should the application port come from `group_vars/web.yml` instead of being hard-coded?**

app_port: 8080 is defined once in group_vars/web/vars.yml, and two places use it:
- the Nginx template: proxy_pass http://127.0.0.1:{{ app_port }}
- the PM2 ecosystem file: PORT: "{{ app_port }}"

If the port were hard-coded in both places, changing it would mean editing two files. Forgetting one would leave Nginx pointing at the wrong port, and the site would return 502 Bad Gateway. With a single variable, both always agree. The roles also stay reusable, because a different environment can use a different port by changing a variable, not the role's code.

---

# Task 8 — Create the `epicbook` Role

## Goal

Create the `epicbook` role to deploy the EpicBook application, connect it to the managed MySQL database, and run the application on port `8080` using PM2.

### Evidence

#### Screenshot 15 — `roles/epicbook/tasks/main.yml` showing application deployment tasks

![ouput](./screenshots/wk9a5t8-ss15.png)

---

#### Screenshot 16 — Task or file showing how the database connection is configured, with secrets hidden

![ouput](./screenshots/wk9a5t8-ss16.png)

---

#### Screenshot 17 — Task or output showing the EpicBook application managed by PM2

![ouput](./screenshots/wk9a5t8-ss17.png)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `epicbook` role?**

The epicbook role gets the application onto the server, connects it to the database and keeps it running. It:
- clones the EpicBook repository with the git module
- installs the Node.js dependencies with community.general.npm
- checks whether the RDS bookstore database already has tables, and imports the schema and seed SQL files only on the first run
- installs PM2 and writes a PM2 ecosystem file (mode 0600) that holds NODE_ENV=production, PORT and the JAWSDB_URL database connection string
- starts EpicBook under PM2 if it isn't already running, and registers PM2 with systemd so the app comes back after a reboot

The app runs as the ubuntu user, not root. A handler reloads it only when the code, dependencies or configuration change.

---

**2. Why is PM2 used for the EpicBook Node.js application?**

If you start Node with node server.js, the app is tied to your SSH session. It stops when you disconnect, and nothing restarts it if it crashes. PM2 is a process manager for Node.js that fixes this:
- Background: it runs the app as a background process.
- Crash recovery: it restarts the app automatically if it crashes.
- Reboots: with pm2 startup and pm2 save, the app starts again on boot.
- Monitoring: pm2 status shows whether the app is online, and pm2 logs epicbook shows its output, which is how I confirmed it was querying the database.
- Reloads: pm2 reload lets Ansible restart it cleanly with new settings.

---

**3. Why should database passwords not be hard-coded in public files?**

Anything committed to Git stays in the repository's history, even after it's deleted later. If the repo is public, or ever becomes public, anyone can find the password and use it to read, change or delete the application's data. Automated bots constantly scan GitHub for leaked credentials.

In my project the password never appears in plain text in a committed file:
- Terraform side: it lives in terraform.tfvars, which .gitignore excludes.
- Ansible side: vars.yml only references "{{ vault_db_password }}", and the real value is in vault.yml, which is encrypted with Ansible Vault.
- On the server: it's written only to the ecosystem file with 0600 permissions.
- In logs: the database tasks use no_log: true, so the password doesn't appear in Ansible's output.

It also makes rotation easier. To change the password, I update it in one encrypted place and re-run the playbook.

---

**4. What does it mean for the application to run on port `8080` while Nginx listens on port `80`?**

There are two separate listeners on the same VM:
- Nginx on port 80: this is the public side, the port browsers use by default for HTTP. The security group allows it from anywhere.
- EpicBook on port 8080: this is the internal side, where Node.js is actually listening. The security group does not open it, so it can't be reached from the internet directly.

A request travels like this:

Browser → http://184.193.19.212:80 → Nginx → 127.0.0.1:8080 → EpicBook (Node.js) → RDS MySQL

Users only ever talk to Nginx, and Nginx passes requests to the app internally. That's why in Task 10 I tested curl http://localhost:8080 on the VM to check the app directly. In Task 11 I tested curl http://184.193.19.212, which goes through Nginx and checks the full public path.

---

# Task 9 — Create Group Variables

## Goal

Create reusable variables for the EpicBook deployment.

The `group_vars/web.yml` file stores values that can be reused across the Ansible roles.

### Evidence

#### Screenshot 18 — `group_vars/web.yml` showing the application, PM2, and database variables, with passwords hidden or masked

![ouput](./screenshots/wk9a5t9-ss18.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `group_vars/web.yml`?**

It holds every configurable value the roles need in one place, kept separate from the tasks that use them. Ansible loads it automatically for every host in the [web] group, because the name matches the inventory group. The roles refer to values like {{ app_port }} or {{ db_host }}, and group_vars provides the actual values.

This keeps the roles generic and reusable. To deploy to a different server or database, I change variables, not role code. It's also where Terraform hands off to Ansible: infrastructure values Terraform created, such as the EC2 public IP and the RDS hostname, are copied here from terraform output so Ansible can configure the app to use them.

---

**2. Which values did you store in `group_vars/web.yml`?**

- Application:
  - app_repo: the EpicBook GitHub repository URL
  - app_branch: main
  - app_dest: /home/ubuntu/theepicbook
  - app_user: ubuntu
  - app_port: 8080
  - pm2_app_name: epicbook
  - node_major: 20, the Node.js version
- Nginx:
  - server_name: 184.193.19.212, the EC2 public IP from Terraform
- Database (from terraform output):
  - db_host: the RDS hostname, epicbook-db.c21gy6iseth9.us-east-1.rds.amazonaws.com
  - db_name: bookstore
  - db_user: bookadmin
  - db_password: "{{ vault_db_password }}", a reference to the Vault, not the real password
  - db_sql_files: the three SQL files to import, in the order they must run


---

**3. How did you handle the database password securely?**

I used Ansible Vault, so the real password is never stored in plain text in the Ansible files:
1. vars.yml only contains a reference: db_password: "{{ vault_db_password }}".
2. The real value is in vault.yml, created with ansible-vault create and encrypted with AES256. Opening it shows only $ANSIBLE_VAULT;1.1;AES256 and scrambled text.
3. When I run the playbook with --ask-vault-pass, Ansible decrypts the password in memory and uses it.

I hit one problem along the way. Ansible only auto-loads group_vars files whose names match an inventory group. A file called group_vars/vault.yml was never loaded, because there is no group called vault, so the password reference stayed unresolved. I fixed this by turning group_vars/web into a folder containing vars.yml and vault.yml. Ansible loads both files for the web group and merges them. I confirmed the fix with ansible web -m debug -a "var=db_password" --ask-vault-pass, which showed the real password.

Other protections:
- On the Terraform side, the password lives in terraform.tfvars, which .gitignore excludes, and its variable is marked sensitive = true.
- The database tasks use no_log: true, so the password never appears in Ansible's output.
- On the server, the password is written only to files with 0600 permissions.
- The mysql commands get the password through the MYSQL_PWD environment variable instead of -p on the command line, where any user on the server could see it in the process list.


---

# Task 10 — Run the Ansible Playbook

## Goal

Run the Ansible playbook to configure the VM and deploy the EpicBook application.

The playbook should run the roles in this order:

1. `common`
2. `nginx`
3. `epicbook`

### Evidence

#### Screenshot 19 — Ansible playbook output showing the roles running

![ouput](./screenshots/wk9a5t10-ss19.png)

---

#### Screenshot 20 — Final Ansible recap showing `failed=0`

![ouput](./screenshots/wk9a5t10-ss20.png)

---

#### Screenshot 21 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become`

![ouput](./screenshots/wk9a5t10-ss21.png)

---

#### Screenshot 22 — Output of `ansible web -i inventory.ini -m command -a "pm2 status"`

![ouput](./screenshots/wk9a5t10-ss22.png)

---

#### Screenshot 23 — Output of `ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"`

![ouput](./screenshots/wk9a5t10-ss23.png)

---

### Notes

Answer the following in your own words:

**1. What command did you run to execute the Ansible playbook?**

From the epicbook-prod/ansible folder:
ansible-playbook site.yml --ask-vault-pass
site.yml runs the common, nginx and epicbook roles in order against the [web] host. --ask-vault-pass prompts for my Vault password so Ansible can decrypt group_vars/web/vault.yml and use the database password. I don't need -i inventory.ini because my ansible.cfg already sets inventory = inventory.ini.

---

**2. How do you know all roles completed successfully?**

The PLAY RECAP at the end showed failed=0 and unreachable=0 for epicbook. That means every task in all three roles ran without an error. A high changed count on the first run is normal, because Ansible was installing packages, cloning the repo and creating config files for the first time.

When I ran the playbook a second time, changed dropped to nearly zero, which shows the roles are idempotent.

But failed=0 only proves Ansible's tasks succeeded, not that the application is healthy. The app could start and then crash, for example on a database error. That's why I checked each layer separately in the next questions.

---

**3. What proves that Nginx is active?**

ansible web -m command -a "systemctl is-active nginx" --ask-vault-pass
This returned active, which comes straight from systemd, the Linux service manager, so it reflects the real state of the service. systemctl is-enabled nginx returned enabled, so Nginx will also start after a reboot. On the VM, ss -tlnp shows Nginx listening on port 80.

---

**4. What proves that PM2 is managing the EpicBook application?**

ansible web -m command -a "pm2 status" --ask-vault-pass
The PM2 process table shows epicbook with status online, running as user ubuntu, with a process ID and an uptime of about 29 minutes. The restart count (↺) is low, at 2. Those two restarts came from the playbook's reload handler and my manual restart, not from crashes. A crashing app would show a climbing restart count or errored.

systemctl is-enabled pm2-ubuntu returned enabled, so PM2 will bring EpicBook back automatically after a reboot. I ran this without --become because the app runs under the ubuntu user's PM2. Root's PM2 list would be empty.

---

**5. What proves that the EpicBook application responds on port `8080`?**

ansible web -m command -a "curl -I http://localhost:8080" --ask-vault-pass
This returned HTTP/1.1 200 OK. The request goes straight to Node.js on port 8080 and skips Nginx, so it proves the application itself is listening and answering. ss -tlnp on the VM also shows the node process listening on port 8080.

A 200 OK alone doesn't prove the database works, so I also checked the app's logs:
ansible web -m command -a "pm2 logs epicbook --lines 20 --nostream" --ask-vault-pass
The logs show real Sequelize queries running against the RDS database, such as SELECT ... FROM Book LEFT OUTER JOIN Author and SELECT count(*) FROM Cart. That confirms the app is reading book data from MySQL, not just returning an empty page.

PS:A note on question 5, for accuracy. Node is listening on all interfaces (*:8080), not only on 127.0.0.1. The app's server.js doesn't specify a listen address. Port 8080 still isn't reachable from the internet, because the security group only allows ports 80 and 22. In earlier answers I described the app as listening on 127.0.0.1:8080; strictly, that's the address Nginx sends requests to, and the security group is what keeps 8080 private. 

---

# Task 11 — Verify the EpicBook Deployment

## Goal

Verify that the EpicBook application is running, accessible in the browser, and connected to the managed MySQL database.

### Evidence

#### Screenshot 24 — Output of `curl -I http://<public_ip>`

![ouput](./screenshots/wk9a5t11-ss24.png)

---

#### Screenshot 25 — Output of the cart API test command

![ouput](./screenshots/wk9a5t11-ss25.png)

---

#### Screenshot 26 — Output of the `/cart` HTTP status check

![ouput](./screenshots/wk9a5t11-ss26.png)

---

#### Screenshot 27 — Browser showing the EpicBook application loaded from `http://<public_ip>`

![ouput](./screenshots/wk9a5t11-ss27.png)
![ouput](./screenshots/wk9a5t11-ss27a.png)

---

### Notes

Answer the following in your own words:

**1. What HTTP response did you receive from the public application URL?**

curl -I http://184.193.19.212
I got HTTP/1.1 200 OK with these headers:
- Server: nginx/1.18.0 (Ubuntu)
- Content-Type: text/html; charset=utf-8
- Content-Length: 24859

The Server: nginx header proves the request went through Nginx on port 80 and wasn't answered directly by Node. The ~24 KB HTML body is the real EpicBook home page, not Nginx's default welcome page. This test covers the full public path, internet → security group (port 80) → Nginx → Node.js on port 8080. The Task 10 checks only tested localhost from inside the VM.

---

**2. What did the cart API test prove?**

curl -X POST http://184.193.19.212/api/cart -H "Content-Type: application/json" -d '{"bookId": 1}'
It returned HTTP 200 and a JSON body for a new cart item ("quantity":1, "price":"28.00"). The body included the full record for book 1, "28 Summers", genre: NYT, pubYear: 2020, AuthorId: 1.

That proves every layer works together in a single request:
- the security group lets traffic in on port 80
- Nginx's proxy_pass correctly forwards an API POST with a JSON body to the app
- the app's /api/cart route handles it
- the app reads from RDS: book 1 exists because the seed SQL was imported
- the app writes to RDS: a new Cart row was created with an ID and timestamps

It also confirms the database credentials from the Vault are correct, since the app couldn't have read or written anything otherwise.

---

**3. What did the `/cart` status check return?**

curl -s -o /dev/null -w "%{http_code}\n" http://184.193.19.212/cart
It returned 200. The cart page loaded successfully through Nginx. It isn't a 502 Bad Gateway, which would mean Nginx couldn't reach the app, a 404, which would mean a routing problem, or a 500, which would mean the app failed, for example on a database query.

I also confirmed that port 8080 is not reachable from outside: curl http://184.193.19.212:8080 timed out. The only public way into the app is through Nginx on port 80, as intended.

---

**4. What issue did you face during verification, and how did you fix it?**

This one has to come from your own experience, so pick whichever issue actually happened to you. Here are drafts for the problems you hit during this assignment:

- Running ansible-playbook -i inventory.ini site.yml --syntax-check from inside roles/common/tasks gave ERROR: the playbook: site.yml could not be found. Ansible looks for site.yml, inventory.ini and ansible.cfg relative to the current folder. I fixed it by running every Ansible command from epicbook-prod/ansible, which also makes sure the project's own ansible.cfg is used.
- The syntax check showed ansible.builtin.apt_repository has been deprecated. Use deb822_repository instead. I replaced the NodeSource repository task in the common role with ansible.builtin.deb822_repository. It also downloads the signing key itself, so I deleted the separate key download task. The Node.js install now refreshes the apt cache only when the repository was just added, which keeps re-runs idempotent.
- the app doesn't use .env. The instructions configure the database through a .env file. When I checked the app's source, I found it doesn't load dotenv. In production, config/config.json reads a single JAWSDB_URL connection string. I passed JAWSDB_URL (mysql://user:password@host:3306/bookstore) to the app through a PM2 ecosystem file with 0600 permissions. The PM2 logs then showed real SQL queries against RDS, and the cart API worked.
- My key was stored on the Windows drive (C:\Users\...\.ssh), where WSL shows the files as readable by everyone. SSH refuses to use a private key with permissions that open. I copied the key into WSL's ~/.ssh and ran chmod 600 on it, then updated public_key_path and ansible_ssh_private_key_file to the new path.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/gFtDWU-g

---

#### Screenshot — Published LinkedIn post

![ouput](./screenshots/wk9a5tlink-ss0.png)

---

# Assignment Questions

Answer the following in your own words:

**1. Why is Terraform used for infrastructure provisioning?**

Terraform defines infrastructure as code. The VPC, subnets, security groups, EC2 instance and RDS database are all described in .tf files instead of being clicked together in the AWS console. That has several benefits:
- Repeatable: I reused my Week-8 code to build a completely new environment with one terraform apply.
- Reviewable: terraform plan shows exactly what will change before anything is touched.
- Versioned: the code can live in Git like any other code.
- Easy to clean up: terraform destroy removes everything, so nothing is forgotten and left running up charges.
- Tracked: the state file lets Terraform update only what changed.

Terraform builds the infrastructure, and Ansible then configures what runs on it.


---

**2. Why are Ansible roles useful for production-style deployments?**

Roles split a deployment into focused pieces with a standard layout: tasks/, templates/, handlers/. Each piece has one job:
- common prepares the operating system
- nginx sets up the reverse proxy
- epicbook deploys and runs the app

This makes the code easier to read and troubleshoot, since I know exactly where to look when Nginx breaks. Roles are reusable, because the nginx role could be dropped into another project and given different variables. Each role can be changed and tested on its own, and site.yml stays a short list that controls the order.

---

**3. What is the purpose of `group_vars/web.yml`?**

It keeps every configurable value in one place, separate from the tasks: the repo URL, paths, app port, server name and database connection details. Ansible loads it automatically for the [web] inventory group, so the roles stay generic and the environment-specific values live here. It's also where Terraform outputs (EC2 IP, RDS hostname) are handed to Ansible.

In my project it's a folder,`group_vars/web.yml`, with vars.yml and an encrypted vault.yml. Both are loaded for the web group, so the password reference resolves.

---

**4. Why should database passwords not be committed to GitHub?**

Git keeps every committed version forever, so a password stays in the history even after it's deleted. Anyone who can see the repo, including the whole internet if it's public, can use it. Bots scan GitHub constantly for leaked credentials. A leaked database password means someone could read, change or delete all the application's data.

I kept the password out of Git in three ways:
- terraform.tfvars is excluded by .gitignore.
- The Ansible copy of the password is encrypted with Ansible Vault.
- Tasks that use the password have no_log: true, so it never shows up in output.

---

**5. What is the purpose of Nginx in this deployment?**

Nginx is the public entry point and reverse proxy. It listens on port 80 and forwards requests to the Node.js app on port 8080. Users visit http://184.193.19.212 without needing a port number. Port 8080 stays closed to the internet (my test against :8080 from outside timed out), and the real client IP and protocol are passed through to the app. Nginx is also where HTTPS, caching or load balancing would be added later.

---

**6. Why should the managed MySQL database not be publicly accessible?**

The database holds all the application's data, and only the application needs to reach it. Mine has publicly_accessible = false, sits in private subnets with no route to the internet, and its security group allows port 3306 only from the web server's security group.

If it were public, anyone could try to connect, guess or brute-force the password, or exploit a MySQL vulnerability. Keeping it private means that even a leaked password couldn't be used from outside the VPC. This is defence in depth: several layers of protection, not just the password.

---

**7. Why is PM2 used for the EpicBook Node.js application?**

Running node server.js directly ties the app to a terminal session. It stops on logout and isn't restarted if it crashes. PM2 handles this:
- it runs EpicBook in the background
- it restarts it automatically on a crash
- it restarts it after a reboot, through the pm2-ubuntu systemd service and pm2 save
- pm2 status shows whether it's online, and pm2 logs shows its output, which is how I confirmed it was querying RDS
- pm2 reload lets Ansible restart it cleanly when the code or configuration changes

---

**8. What does idempotency mean in Ansible?**

Idempotency means running the same playbook more than once leaves the server in the same final state, and only changes what isn't already correct. Ansible modules check the current state first: if Nginx is already installed or the config file already matches, they report ok and do nothing.

My roles were designed for this:
- The SQL import runs only when SHOW TABLES returns nothing, so seed data is never inserted twice.
- PM2 starts the app only if it isn't already registered.
- Handlers reload Nginx and the app only when something actually changed.

The proof is that the second run of site.yml showed changed close to zero.

---

**9. What issue did you face during the deployment, and how did you fix it?**

As with Task 11, choose the one you actually experienced. Here's a draft using the most valuable lesson:

The instructions configured the database connection through a .env file, but the application ignored it. When I checked the source, I found EpicBook doesn't load dotenv. In production, config/config.json reads a single JAWSDB_URL connection string. I fixed it by passing JAWSDB_URL (mysql://user:password@host:3306/bookstore) to the app through a PM2 ecosystem file with 0600 permissions. The PM2 logs then showed real SQL queries against RDS, and the cart API returned real book data.

There was also a related trap. Terraform's rds_endpoint output includes :3306 at the end, which would have broken db_host. So I added a db_address output that gives only the hostname.

The lesson: inspect how the application actually reads its configuration before deciding how to deploy it.

Other options you can use instead: running commands from the wrong folder (site.yml could not be found), the apt_repository deprecation fixed with deb822_repository, or the SSH key permissions fix described in the Task 11 answer.

---

**10. What security improvement would you make before using this setup in production?**

The most important improvement is adding HTTPS. Right now everything travels over plain HTTP on port 80, so anyone on the network path can read or tamper with the traffic. I'd point a domain at the server, get a free TLS certificate from Let's Encrypt with Certbot, have Nginx serve port 443, and redirect HTTP to HTTPS.

Other improvements I'd make:
- A dedicated database user for the app. EpicBook currently connects as the RDS admin user bookadmin. A dedicated user with access only to the bookstore database follows least privilege.
- Encrypted, backed-up storage for RDS. Set storage_encrypted = true, turn on automated backups (currently backup_retention_period = 0), and enable deletion protection.
- Remote, encrypted Terraform state. The state file stores the database password in plain text. Keep it in an encrypted S3 bucket with DynamoDB locking instead of on my laptop.
- Remove public SSH. Use AWS Systems Manager Session Manager, or a bastion host, instead of opening port 22. Also turn host_key_checking back on, with known host keys.
- A managed secret store. Keep the database password in AWS Secrets Manager, so it can be rotated automatically.

---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [x] `README.md`
- [x] Terraform files under either `terraform/azure/` or `terraform/aws/`
- [x] `ansible/ansible.cfg`
- [x] `ansible/inventory.ini`
- [x] `ansible/site.yml`
- [x] `ansible/group_vars/web.yml`
- [x] `ansible/roles/common/tasks/main.yml`
- [x] `ansible/roles/nginx/tasks/main.yml`
- [x] `ansible/roles/nginx/templates/epicbook.conf.j2`
- [x] `ansible/roles/epicbook/tasks/main.yml`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots.
- Mention the cloud provider used: Azure or AWS.
- Add the VM public IP address.
- Add the final application URL.
- Add Terraform output proof.
- Add Ansible role tree proof.
- Add all required notes and assignment question answers.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, Terraform state files, subscription IDs, or account IDs.

---

# Completion Checklist

- [x] Task 1: Project folder layout created
- [x] Task 2: Terraform infrastructure provisioned
- [x] Task 3: SSH key-based access verified
- [x] Task 4: Ansible inventory and configuration created
- [x] Task 5: Main Ansible playbook created
- [x] Task 6: `common` role created
- [x] Task 7: `nginx` role created
- [x] Task 8: `epicbook` role created
- [x] Task 9: Group variables created
- [x] Task 10: Ansible playbook run completed
- [x] Task 11: EpicBook deployment verified
- [x] Terraform files created under only one cloud provider folder
- [x] One Ubuntu VM was created
- [x] One managed MySQL database was created
- [x] SSH port `22` is restricted to the controller public IP
- [x] HTTP port `80` is accessible
- [x] MySQL port `3306` is not publicly open
- [x] `ansible web -i inventory.ini -m ping` returns `SUCCESS`
- [x] `site.yml` calls the roles in the correct order
- [x] Database secrets are hidden or handled securely
- [x] Nginx is active
- [x] PM2 shows the EpicBook application running
- [x] EpicBook responds on port `8080`
- [x] Public URL loads in the browser
- [x] Cart API verification works
- [x] Playbook completes with `failed=0`
- [x] Screenshots 1–27 are included
- [x] Assignment questions are answered
- [x] LinkedIn post published
- [x] LinkedIn post URL added
- [x] No sensitive information is exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*