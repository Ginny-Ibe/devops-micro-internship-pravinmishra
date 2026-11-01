# Assignment 3 — Docker Networking

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will explore Docker networking by using default bridge, custom bridge, multiple bridge, and host network modes. You will verify container communication, service discovery, network isolation, public access, and host networking on a Linux VM or EC2 instance.

---

# Task 1 — Deploy a Standalone Application Using the Default Bridge Network

## Goal

Deploy an Nginx web server using Docker’s default bridge network and access it through the VM public IP address.

### Evidence

#### Screenshot 1 — Available Docker Networks

Add a screenshot of the terminal showing:

```bash
docker network ls
```

The output must include the default `bridge`, `host`, and `none` networks.

![ouput](./screenshots/wk11a3t1-ss1.png)

---

#### Screenshot 2 — Nginx Image Pull

Add a screenshot of the terminal showing successful completion of:

```bash
docker pull nginx:alpine
```

![ouput](./screenshots/wk11a3t1-ss2.png)

---

#### Screenshot 3 — Running `myweb` Container

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the running `myweb` container with:

```text
0.0.0.0:80->80/tcp
```

![ouput](./screenshots/wk11a3t1-ss3.png)

---

#### Screenshot 4 — Nginx Welcome Page

Add a browser screenshot showing the Nginx Welcome Page at:

```text
http://3.237.253.252
```

Ensure that the VM public IP is visible in the address bar. Add your full name as a clear caption directly below the screenshot.

![ouput](./screenshots/wk11a3t1-ss4.png)

---

# Task 2 — Connect Containers Using a Custom Bridge Network

## Goal

Create a custom bridge network and verify that containers can communicate using container names rather than IP addresses.

### Evidence

#### Screenshot 5 — Custom Bridge Network Created

Add a screenshot of the terminal showing `mynetwork` in:

```bash
docker network ls
```

![ouput](./screenshots/wk11a3t2-ss5.png)

---

#### Screenshot 6 — Running `web` and `client` Containers

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show both `web` and `client` containers running without published host ports.

![ouput](./screenshots/wk11a3t2-ss6.png)

---

#### Screenshot 7 — Service Discovery by Container Name

Add a screenshot of the terminal showing successful output from:

```bash
docker exec client wget -qO- http://web
```

The output must display the Nginx Welcome Page HTML.

![ouput](./screenshots/wk11a3t2-ss7.png)

---

#### Screenshot 8 — Custom Network Inspection

Add a screenshot of the terminal showing:

```bash
docker network inspect mynetwork
```

The output must show both `web` and `client` connected to `mynetwork`.

![ouput](./screenshots/wk11a3t2-ss8.png)
![ouput](./screenshots/wk11a3t2-ss8a.png)

---

# Task 3 — Demonstrate Multi-Network Isolation

## Goal

Deploy frontend, backend, and database containers across two separate Docker networks. Verify allowed communication and confirm that the frontend cannot directly reach the database.

### Evidence

#### Screenshot 9 — Two Docker Networks

Add a screenshot of the terminal showing:

```bash
docker network ls
```

The output must include both `frontend-network` and `backend-network`.

![ouput](./screenshots/wk11a3t3-ss9.png)

---

#### Screenshot 10 — Running Multi-Network Containers

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show:

- `frontend` with the published port mapping `0.0.0.0:80->80/tcp`
- `backend` without a published host port
- `db` without a published host port

![ouput](./screenshots/wk11a3t3-ss10.png)

---

#### Screenshot 11 — Frontend Network Inspection

Add a screenshot of the terminal showing:

```bash
docker network inspect frontend-network
```

The output must show `frontend` and `backend`.

![ouput](./screenshots/wk11a3t3-ss11.png)

---

#### Screenshot 12 — Backend Network Inspection

Add a screenshot of the terminal showing:

```bash
docker network inspect backend-network
```

The output must show `backend` and `db`.

![ouput](./screenshots/wk11a3t3-ss12.png)

---

#### Screenshot 13 — Frontend-to-Backend Communication

Add a screenshot of the terminal showing successful output from:

```bash
docker exec frontend wget -qO- http://backend
```

The output must display the Nginx Welcome Page HTML.

![ouput](./screenshots/wk11a3t3-ss13.png)

---

#### Screenshot 14 — Backend-to-Database Communication

Add a screenshot of the terminal showing a successful connection to `db` on port `27017` from the `backend` container.

![ouput](./screenshots/wk11a3t3-ss14.png)

---

#### Screenshot 15 — Frontend-to-Database Isolation

Add a screenshot of the terminal showing that the `frontend` container cannot reach `db` on port `27017`.

The output must include:

```text
Expected result: frontend cannot reach db
```

![ouput](./screenshots/wk11a3t3-ss15.png)

---

#### Screenshot 16 — Public Frontend Access

Add a browser screenshot showing the Nginx Welcome Page from the `frontend` container at:

```text
http://3.237.253.252
```

Ensure that the VM public IP is visible in the address bar. Add your full name as a clear caption directly below the screenshot.

![ouput](./screenshots/wk11a3t3-ss16.png)

---

# Task 4 — Deploy an Application Using Docker Host Network Mode

## Goal

Run an Nginx container using Docker host network mode and compare it with bridge networking.

### Evidence

#### Screenshot 17 — Running Host-Networked Container

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the running `fastapp` container.

![ouput](./screenshots/wk11a3t4-ss17.png)

---

#### Screenshot 18 — Host Network Mode Verification

Add a screenshot of the terminal showing output from:

```bash
docker inspect fastapp | grep '"NetworkMode"'
```

The output must confirm:

```text
"NetworkMode": "host"
```

![ouput](./screenshots/wk11a3t4-ss18.png)

---

#### Screenshot 19 — Host-Networked Nginx Page

Add a browser screenshot showing the Nginx Welcome Page at:

```text
http://3.237.253.252/
```

Ensure that the VM public IP is visible in the address bar. Add your full name as a clear caption directly below the screenshot.

![ouput](./screenshots/wk11a3t4-ss19.png)

---

#### Screenshot 20 — Host-Networked Container Cleanup

Add a screenshot of the terminal showing successful completion of:

```bash
docker stop fastapp
docker rm fastapp
```

![ouput](./screenshots/wk11a3t4-ss20.png)

---

# Networking Notes

Write a short note explaining:

- Default bridge networking
  
- Container-name communication on a custom bridge network
- Why the frontend could not access the database in Task 3
- The difference between bridge mode and host network mode

**Docker Networking Note**
1. Default Bridge Networking
When a container starts without a `--network` option, Docker attaches it to the default **bridge** network. In Task 1 that is where `myweb` ran. Each container on this network gets its own private IP address and is isolated from the host. The application is only reachable from outside if a port is published with `-p`. For example, `-p 80:80` forwards host port 80 to the container's port 80.
The default bridge has no built-in DNS, so containers on it can only reach each other by IP address. IP addresses can change when a container is recreated, which makes them unreliable.

2. Container-Name Communication on a Custom Bridge Network
A user-defined bridge network, such as `mynetwork` in Task 2, includes Docker's internal DNS. Any container on that network can reach another one by using its container name as a hostname. For example:

```bash
docker exec frontend curl -s http://backend
docker exec client wget -qO- http://web
```

Both commands worked without anyone looking up an IP address. The `backend` and `web` containers had no published ports, but other containers could still reach them on `mynetwork`. That shows a custom network lets containers talk to each other privately without exposing them to the host.

3. Why the Frontend Could Not Access the Database in Task 3
In Task 3 the containers were spread across two networks:

| Container | Role | frontend-network | backend-network |
|---|---|:---:|:---:|
| `ui` | Frontend | ✔ | ✘ |
| `api` | Backend | ✔ | ✔ |
| `db` | MongoDB | ✘ | ✔ |

Two containers can only communicate if they share at least one network. `ui` and `db` had no network in common, so `ui` could not resolve the name `db` or open a connection to it. Only `api`, which joined both networks, could reach both of them.

The database also had no published port (`-p`), so the host could not reach it either. This setup follows the security principle that the user-facing tier should never talk to the database directly. All data access goes through the backend:

**ui → api → db**

4. Bridge Mode vs Host Network Mode

| | Bridge mode | Host mode (`--network host`) |
|---|---|---|
| Network stack | The container gets its own isolated network and IP address | The container uses the host's network directly |
| Port access | Needs `-p host:container` to be reachable from outside | No `-p` needed. The app listens straight on the host's ports |
| Isolation | Isolated from the host network | No network isolation |
| Port conflicts | Different host ports can be mapped (e.g. 8080→80) | Fails if the host port is already in use |
| Performance | Small overhead from network address translation (NAT) | Slightly faster because there is no NAT layer |

In Task 4, `fastapp` ran with `--network host` and was reachable on `http://localhost` without any port mapping. `docker inspect fastapp` showed `"NetworkMode": "host"`.

Bridge mode is the safer default. Host mode suits cases that need maximum network performance or direct access to the host's network, at the cost of isolation.

---

# LinkedIn Requirement

## Goal

Create a LinkedIn post about the Docker networking modes explored, one key lesson about container isolation, and the learning outcomes from this assignment.

### Evidence

#### LinkedIn Post URL

<https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-devops-docker-ugcPost-7520360924336132096-AobT/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGTqulMBvpSBQMnxbzFBrJkA0C9nlWM_uqM>

---

#### LinkedIn Post Screenshot

![ouput](./screenshots/wk11a3tlink-ss0.png)

---

# Submission Instructions

- Complete all tasks in sequence.
- Include Screenshots 1–20 exactly as specified.
- Include the Networking Notes section.
- Include the LinkedIn post URL and screenshot.
- Ensure that your full name is visible in all terminal screenshots.
- Add your full name as a caption below each browser screenshot that shows the standard Nginx page.
- Do not expose private keys, passwords, access keys, tokens, account IDs, or other sensitive information.

---

# Completion Checklist

- [x] Completed on a Linux VM or EC2 instance
- [x] Docker Engine is running
- [x] HTTP port 80 is allowed in the VM firewall or cloud security rules
- [x] Default bridge networking verified
- [x] Custom bridge network created
- [x] Container-name communication verified
- [x] `frontend-network` and `backend-network` created
- [x] Frontend-to-backend communication verified
- [x] Backend-to-database communication verified
- [x] Frontend-to-database isolation verified
- [x] Only the frontend published port 80 in Task 3
- [x] Host network mode verified
- [x] All required screenshots included
- [x] Networking Notes completed
- [x] LinkedIn post URL and screenshot included
- [x] Full name visible in terminal screenshots
- [x] Browser screenshots include full-name captions
- [x] No sensitive information exposed

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
